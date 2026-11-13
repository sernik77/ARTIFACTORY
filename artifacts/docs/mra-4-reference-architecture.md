---
title: "MRA-4: Minimal Recursive Agent Reference Architecture"
kind: docs
status: stable
summary: "Buildable reference architecture for a 4-state recursive agent (think, act, evaluate, change_approach) with a Go core (in Polish)."
tags: [agents, go, state-machine]
created: 2026-06-02
updated: 2026-06-02
origin: "https://docs.google.com/document/d/13vIfFss6RTBZWeRXATr713pFSmSV3zkN6eQQn6shuSE"
---

# 08 · MRA-4 (reference architecture)

**Minimal Recursive Agent — 4 states.** Konkretna, budowalna implementacja łącząca narzędzia (`04`), pamięć (`05`) i teorię pętli (`06`). Rdzeń w **Go** — single binary, `context`-based kill switch, idiomatic state machine w `switch`. Bash dla env/narzędzi, node dla skryptów ad-hoc. Poprawiona wersja draftu MRA-4 z domkniętymi dziurami.

---

## TL;DR

- **4 stany robocze** — `think · act · evaluate · change_approach` — **+ 2 terminalne** `finished · failed`.  
- `THINK→THINK` strukturalnie zabronione — THINK musi wyjść z akcją, zmianą podejścia lub zakończeniem. Bije rumination bez licznika.  
- **Per-step `Attempts`** (pole w `AtomicStep`), nie globalny `fail_count`.  
- **`THINK` buduje i aktualizuje plan** — naprawiony crash `plan == nil` przy pierwszym `ACT`.  
- **Kill switch przez `context.Context`** — `context.WithCancel` + nasłuch na plik `STOP` lub `SIGINT` → czyste przerwanie z każdej iteracji.  
- Driver (32B) do pełnego THINK i CHANGE\_APPROACH; worker (8B) do EVALUATE i fast-path THINK.

---

## Changelog vs oryginalny draft

| \# | Oryginalny MRA-4 | Problem | Fix |
| :---- | :---- | :---- | :---- |
| 1 | `while True`, brak terminali | Plan się kończy → nigdzie iść; CHANGE\_APPROACH pętla ∞ | `finished` / `failed` + `MaxApproaches` |
| 2 | THINK może wybrać THINK | Rumination loop, żaden guardrail tego nie łapał | **THINK→THINK strukturalnie zabronione** |
| 3 | `llm_think()` zwraca tylko `{next_state,...}`; plan budowany tylko w CHANGE\_APPROACH | `plan == nil` → crash na pierwszym ACT | THINK output niesie `PlanUpdate` + `Action` |
| 4 | Globalny `fail_count` | 3 faile na 3 różnych krokach odpala CHANGE\_APPROACH | Liczenie na `AtomicStep.Attempts` (per-step) |
| 5 | `history.append(result)` wpychany w całości do THINK | Overflow kontekstu → agent powtarza błędy | Lean `AssembleContext` + rolling summaries (`05`) |
| 6 | ACT wolność = "średnia", ale pętla dequeue deterministycznie | Sprzeczność: kto wybiera akcję? | **THINK wybiera, ACT tylko wykonuje** |
| 7 | Pełny driver-model THINK po każdym sukcesie | ~13 s reload na 32B MoE przy `KEEP_ALIVE=0` (`02`) | Worker + fast-path THINK po czystym sukcesie |
| — | `confidence` nieużywane | Martwe pole | Podpięte: niski conf → eskalacja do full THINK / approval |
| — | Python / TypeScript | Off-stack | **Go** (rdzeń), bash (env/narzędzia), node (skrypty) |

---

## Stany

| Stan | Odpowiedzialność | Wolność LLM | Model |
| :---- | :---- | :---- | :---- |
| **THINK** | Rozumowanie, plan, refleksja, wybór następnej konkretnej akcji | Duża (wewnątrz calla) | driver (lub worker na fast-path) |
| **ACT** | Wykonanie dokładnie tego co wybrał THINK. Nic więcej. | Brak — mechaniczny | — |
| **EVALUATE** | Ocena rezultatu, routing | Mała (klasyfikacja) | worker |
| **CHANGE\_APPROACH** | Porzucenie strategii, generowanie nowego planu od góry | Duża | driver |
| `finished` | Plan kompletny / cel osiągnięty | — | — |
| `failed` | Nieodwracalne: budżet podejść, malformed THINK, globalny limit | — | — |

---

## Twarde guardrailsy (system nadpisuje LLM)

1. `ACT` → zawsze `EVALUATE`. Bez wyjątku.  
2. Po failu w `EVALUATE` → `THINK` lub `CHANGE_APPROACH`, **nigdy** prosto do `ACT`.  
3. `AtomicStep.Attempts >= MaxAttempts (3)` → wymuszony `CHANGE_APPROACH`. Per-step.  
4. `THINK` nie może wybrać `THINK`. Structurally forbidden. (Guardrail 4 eliminuje potrzebę streak-countera.)  
5. `CHANGE_APPROACH` count `> MaxApproaches (4)` → `FAILED`. Eskalacja do człowieka.  
6. Globalny `MaxSteps (500)` → `FAILED`. Backstop.  
7. Plan kompletny → `FINISHED`.

>   
> Loop-level rules (1–7) żyją tutaj. Env-level guardrailsy (command whitelist, approval, network, kill switch) żyją w `07`. Nie mieszać.

---

## THINK — structured output (kontrakt)

{

  "next\_state":   "act | change\_approach | finished",

  "reasoning":    "dlaczego ten ruch",

  "confidence":   0.85,

  "plan\_update":  null,

  "action": {

    "step\_id": "1.2.3",

    "tool":    "run\_shell",

    "args":    { "cmd": "ls -la /work" }

  }

}

`EVALUATE` output (worker model):

{ "success": true, "summary": "12 plików", "observations": \["..."\], "risk": "low" }

---

## Struktury danych (Go)

type AtomicStep struct {

    ID           string   // "1.2.3" — adresowalny dla replan + resume

    Description  string

    Status       string   // "pending" | "in\_progress" | "done" | "failed"

    Attempts     int      // ← guardrail 3 liczy na TYM, nie na globalnym fail\_count

    Action       \*Action

    Result       string

    Observations \[\]string

}

type HighLevelStep struct {

    ID          string

    Description string

    Status      string

    Atomic      \[\]\*AtomicStep

}

type Subgoal  { ID, Description string; Steps    \[\]\*HighLevelStep }

type Concept  { ID, Description string; Subgoals \[\]\*Subgoal       }

type Plan struct {

    Goal         string     // przypięty, niezmienny — obrona przed driftem (05)

    Concepts     \[\]\*Concept

    CurrentFocus string     // node id "1.2.3", NIE luźny map

}

// metody: NextLeaf(), Bind(action), MarkDone(step), Apply(update), Complete()

type Action struct {

    StepID string         \`json:"step\_id"\`

    Tool   string         \`json:"tool"\`

    Args   map\[string\]any \`json:"args"\`

}

type ThinkDecision struct {

    NextState  string      \`json:"next\_state"\`

    Reasoning  string      \`json:"reasoning"\`

    Confidence float64     \`json:"confidence"\`

    PlanUpdate \*PlanUpdate \`json:"plan\_update,omitempty"\`

    Action     \*Action     \`json:"action,omitempty"\` // wymagane gdy NextState == "act"

}

type EvalVerdict struct {

    Success      bool     \`json:"success"\`

    Summary      string   \`json:"summary"\`

    Observations \[\]string \`json:"observations"\`

    Risk         string   \`json:"risk"\`

}

---

## Główna pętla (Go)

// ── Stałe (LLM nie może ich nadpisać) ────────────────────────────────────────

const (

    MaxAttempts   = 3

    MaxApproaches = 4

    MaxSteps      = 500

    ConfMin       = 0.4

)

// ── Deps — dependency injection, zero globals ─────────────────────────────────

type Deps struct {

    Driver LLMClient    // 32B — pełny THINK + CHANGE\_APPROACH

    Worker LLMClient    // 8B  — EVALUATE + fast-path THINK

    Tools  ToolExecutor // shell/fs/db/net z guardrail-hookami (07)

    Memory MemoryLayer  // assembleContext + append + loadPlan (05)

    Log    func(state string, data map\[string\]any)

}

func RunMRA4(ctx context.Context, goal string, d Deps) (Result, error) {

    state := "think"

    plan, \_ := d.Memory.LoadPlan(goal)

    var step   \*AtomicStep

    var result \*ActionResult

    approaches := 0

    for budget := MaxSteps; budget > 0; budget-- {

        // ── Kill switch — context anulowany (SIGINT, plik STOP, timeout) ─────

        select {

        case \<-ctx.Done():

            return Result{State: "failed", Reason: "killed"}, ctx.Err()

        default:

        }

        switch state {

        // ── THINK — jedyne miejsce podejmowania decyzji ───────────────────────

        case "think":

            mctx := d.Memory.AssembleContext(goal, plan, step)

            fast := result != nil && result.Success && plan.NextLeaf() != nil

            model := d.Driver

            if fast { model = d.Worker }

            dec, err := model.Think(ctx, mctx)

            if err != nil { return Result{State: "failed", Reason: err.Error()}, nil }

            d.Log("think", map\[string\]any{"next": dec.NextState, "conf": dec.Confidence})

            if dec.PlanUpdate != nil {

                plan = plan.Apply(dec.PlanUpdate)

            }

            if plan == nil || plan.Complete() { state = "finished"; continue }

            switch dec.NextState { // "think" nie jest legalnym wyborem (guardrail 4)

            case "finished":

                state = "finished"

            case "change\_approach":

                state = "change\_approach"

            case "act":

                if dec.Action == nil { state = "failed"; continue } // malformed; prod: 1 reprompt

                if fast && dec.Confidence \< ConfMin { result = nil; continue } // bounce → driver

                step = plan.Bind(dec.Action)

                state = "act"

            default:

                state = "failed"

            }

        // ── ACT — mechaniczne wykonanie, zero decyzji ─────────────────────────

        case "act":

            r, err := d.Tools.Execute(ctx, step.Action) // env guardrailsy wewnątrz (07)

            if err != nil { return Result{State: "failed", Reason: err.Error()}, nil }

            result = r

            d.Memory.Append(step, result) // metadata: success/cost/risk (05)

            state = "evaluate"             // guardrail 1: ACT → zawsze EVALUATE

        // ── EVALUATE — ocena + routing (nigdy prosto do ACT po failu) ─────────

        case "evaluate":

            v, err := d.Worker.Evaluate(ctx, step, result)

            if err != nil { return Result{State: "failed", Reason: err.Error()}, nil }

            d.Log("evaluate", map\[string\]any{"success": v.Success, "summary": v.Summary})

            if v.Success {

                plan.MarkDone(step)

                state = "think" // fast-path wejdzie w następnym THINK

            } else {

                step.Attempts++                                    // per-step (guardrail 3)

                if step.Attempts >= MaxAttempts {

                    state = "change\_approach"                      // guardrail 3

                } else {

                    state = "think"                                // guardrail 2: nie ACT

                }

            }

        // ── CHANGE\_APPROACH — porzucenie strategii, nowy plan (z budżetem) ────

        case "change\_approach":

            approaches++

            if approaches > MaxApproaches { // guardrail 5 → eskalacja do człowieka

                return Result{State: "failed", Reason: "approaches\_exhausted"}, nil

            }

            d.Log("change\_approach", map\[string\]any{"n": approaches})

            mctx := d.Memory.AssembleContext(goal, plan, step)

            newPlan, err := d.Driver.NewApproach(ctx, goal, mctx)

            if err != nil { return Result{State: "failed", Reason: err.Error()}, nil }

            plan = newPlan

            step, result = nil, nil

            state = "think"

        case "finished", "failed":

            return Result{State: state, Plan: plan, Approaches: approaches}, nil

        }

    }

    return Result{State: "failed", Reason: "step\_budget\_exhausted"}, nil // guardrail 6

}

### Kill switch z zewnątrz

ctx, cancel := context.WithCancel(context.Background())

// plik STOP — pasuje do 07's "one trivial external action stops the loop"

go func() {

    for {

        if \_, err := os.Stat("STOP"); err == nil { cancel(); return }

        time.Sleep(500 \* time.Millisecond)

    }

}()

// albo sygnał

go func() {

    c := make(chan os.Signal, 1)

    signal.Notify(c, os.Interrupt, syscall.SIGTERM)

    \<-c; cancel()

}()

result, err := RunMRA4(ctx, goal, deps)

### Ollama client (stdlib only, zero deps)

// POST http://localhost:11434/api/chat — net/http ze standardowej biblioteki

func (c \*OllamaClient) Think(ctx context.Context, mctx ModelContext) (\*ThinkDecision, error) {

    body, \_ := json.Marshal(map\[string\]any{

        "model":    c.Model,

        "messages": mctx.Messages,

        "tools":    mctx.Tools,

        "stream":   false,

    })

    req, \_ := http.NewRequestWithContext(ctx, "POST", c.BaseURL+"/api/chat",

                                         bytes.NewReader(body))

    req.Header.Set("Content-Type", "application/json")

    resp, err := http.DefaultClient.Do(req)

    // ... parse resp.Body → ThinkDecision

}

---

## Pamięć — `AssembleContext` (lean, nie raw history)

// assembleContext składa THINK context:

//   - przypięty goal (drift defense, 05)

//   - current node / sub-goal

//   - ostatnie N kroków (working memory)

//   - top-k episodic recall dla bieżącego węzła

//   - rolling summaries ukończonych gałęzi

// NIE wpycha całej historii — patrz 05 na schema + rolling summaries.

Agent może z THINK zawołać `recall`/`search_memory` dla głębszego sięgnięcia, ale essentials są auto-wstrzyknięte, więc nie musi — nie działa "na ślepo" gdy zapomni o recall.

---

## Podział języków

| Co | Język | Plik |
| :---- | :---- | :---- |
| Rdzeń: pętla, plan, guardrailsy, Ollama client, memory | **Go** | `mra4.go`, `plan.go`, `memory.go`, `ollama.go` |
| Env / setup / glue | **bash** | `ollama-env.sh`, `tools.sh` |
| Shell/fs narzędzia agenta | **bash** (`os/exec` je wołanie) | `tools/shell.sh` |
| Ad-hoc skrypty, JSON transforms, benchmark | **node.js** | `scripts/*.mjs` |
| Training (Unsloth / Axolotl) | **Python** | izolowany w `03-tuning` |

---

## Spójność z resztą notatek

- **`04-tools`**: `d.Tools.Execute(ctx, step.Action)` to ta warstwa; guardrail-hoki i `shell/fs/db/net` surfaces tam.  
- **`05-memory`**: `d.Memory.*` — goal pinning, drift, summarization, schema metadanych.  
- **`06-states-and-rules`**: MRA-4 to konkretna realizacja hierarchicznego wariantu z `06`; budżety i nazwy (`MaxAttempts`, `MaxSteps`, `NextLeaf`, `MarkDone`) spójne.  
- **`07-sandbox-and-observability`**: każdy `ACT` przechodzi przez env guardrailsy; każde `d.Log(...)` to trace obserwowalności i przyszły ReAct training data (`03`).

---

## Open questions

- Malformed THINK (`next_state: act` bez `action`): od razu `failed`, czy jeden reprompt? Jeden reprompt jest łagodniejszy; potrzebuje małego licznika żeby nie loopować.  
- Fast-path threshold: czy "ostatni krok sukces + jest następny leaf" wystarczy, czy też bramkować przez głębokość planu / gęstość ostatnich faili?  
- Section-level CHANGE\_APPROACH: guardrail 3 jest per-atomic-step. Czy dodać regułę "cały HighLevelStep spalił N atomic stepów" → zmień podejście poziom wyżej?  
- `confidence` jest self-reported przez LLM — czy jest wystarczająco skalibrowane do bramkowania, czy lepiej użyć obserwowalnych sygnałów (recent fail rate)?
