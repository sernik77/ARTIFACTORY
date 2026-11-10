---
title: "Claude's Token Economy"
kind: knowledge
status: stable
summary: "Tips and tricks for Claude usage, effort levels and token efficiency."
tags: [claude, tokens, efficiency, effort, claude-code]
created: 2026-11-10
updated: 2026-11-10
origin: "https://claude.ai/artifact/NXr6Ujo14614JVHu5KzkYh"
---

# Claude's Token Economy

*Tips & Tricks for Claude Usage/Effort/Efficiency (Anthropic)*

2026-11-10 · sernik

## TL;DR

Medium is the official default on Opus 5.5 and Haiku 5.5, and it is a genuine efficiency sweet spot — but not everywhere. High pays off measurably on agentic and terminal coding and on tasks with hidden edge cases. Extra (xhigh) earns its cost only on long autonomous runs. Max is rarely worth the price.

- **Medium is the default and the sweet spot on Opus 5.5, but not universally.** On the Opus 5.5 launch benchmarks, Medium scores highest on FrontierCode (54.6% vs 54.4% at Max). On Terminal-Bench 4.0 the Medium-to-High step is roughly +6.6 points for roughly +32% cost, so High is worth it there. Max on TB 4.0 scores nominally lower than xhigh (64.8% vs 66.4%), though third-party summaries of the system card place that gap inside the ±2.6-point standard error. In Artificial Analysis Intelligence Index v4.3.2 (measured at launch, 22 Sep 2026), Max cost $5.98 per task against $1.34 for Medium — about 4.46×.
- **Usage multipliers are not officially documented.** The Help Center states only that higher effort uses more tokens and that you will reach usage limits faster. The `/effort` slider in Claude Code shows an estimated cost relative to Medium for Opus 5.5 (as transcribed by the community): ~0.7× / 1× / ~1.5× / ~2.5× / ~5.2×. Artificial Analysis Index v4.3.2 gives a similar picture: low $0.55, medium $1.34, high $1.82, xhigh $3.46, max $5.98 — roughly 0.4× / 1× / 1.4× / 2.6× / 4.5×. The newer Index v5 (27 Oct 2026) reports different figures.
- **Context management saves far more than effort tuning.** What matters is a stable prompt cache, `/clear` between tasks, a lean CLAUDE.md (under 200 lines, per the official recommendation), Haiku or Sonnet subagents at low effort for exploration, CLI over MCP, and hooks that filter tool output. Switching models, running `/compact`, or connecting an MCP server mid-task all break the cache, and the next turn pays full price for the entire context.

## Key findings

1. **Effort is a behavioral signal, not a token budget.** The API documentation states that effort is a behavioral signal rather than a strict token budget. It affects all output tokens: thinking, tool calls and text. The documentation adds that lower effort also means fewer and terser tool calls. The Claude Code blog post by Lydia Hallie (7 Jul 2026) adds that effort also governs how many files Claude reads, how much it verifies, and how far it pushes a task before returning to you.
2. **Effort does not shorten the visible reply.** For Opus 5 and Fable 5.2 the documentation says explicitly that effort changes the amount of thinking, not the length of the visible answer. Reply length is controlled through the prompt.
3. **Defaults differ per model and per surface.** On the API, Opus 5.5 and Haiku 5.5 default to `medium` and everything else to `high`, including Sonnet 5.5 and Fable 5.2. In Claude Code, Opus 5.5, Sonnet 5.5 and Haiku 5.5 default to `medium`, Opus 4.7 to `xhigh`, and the rest to `high`.
4. **Level names are not comparable across models.** Anthropic states that Opus 5.5 at `medium` matches or beats Opus 5 at `high`, and that on several coding evals even `low` comes close. Do not carry effort settings from one model to another.
5. **Effort fixes missed edge cases, not a wrong approach.** Thariq Shihipar of Anthropic, writing in "Spending your effort" (25 Sep 2026), notes that raising effort tends to reduce failures caused by missed edge cases but does not fix cases where the model has the wrong approach. A wrong approach is fixed by a better model or better context.

## 1\. What effort actually does

| Aspect | What is known | Status |
|---|---|---|
| API parameter | `output_config.effort`: `low` / `medium` / `high` / `xhigh` / `max` | Official |
| Thinking | Adaptive thinking. At `low` Claude may skip thinking on simple tasks; at `max` it thinks with no constraints on thinking depth | Official |
| Tool calls | Lower effort means fewer and terser tool calls | Official |
| Files read and verification | Higher effort means more reads, tests and double-checks. Lower effort means Claude would rather ask you for more context | Official (Claude Code blog) |
| Reply length | Effort barely changes how long replies are (Fable 5.2) | Official |
| Sampling (temperature and similar) | No documentation that effort changes sampling | Undocumented |
| `budget_tokens` | On Opus 5.5 and Fable 5.1+, `budget_tokens` and `thinking: disabled` return 400. Effort is the only control | Official |
| `max_tokens` | A hard ceiling on thinking plus text. At high and above, set it generously (Sonnet 5.5 agentic coding: 128,000) | Official |
| Changing effort mid-conversation | Per-message `output_config` (beta) preserves the prompt cache on Opus 5.5, Sonnet 5.5, Haiku 5.5, Fable 5.x and Opus 5. Changing top-level effort invalidates the cache | Official |
| `MAX_THINKING_TOKENS` | Applies only to models with a fixed thinking budget. Adaptive models ignore non-zero values | Official |

**Thinking support per model**

- **Opus 5.5:** always on. `disabled` returns 400.
- **Sonnet 5.5:** `disabled` errors. Instead there is `thinking: {"type": "between_tools"}`, which works only at low, medium and high.
- **Haiku 5.5 and Opus 5:** `disabled` is accepted up to and including `high`; at xhigh and max it returns 400.

## 2\. Model support: levels and defaults

| Model | low | medium | high | xhigh (Extra) | max | API default | Claude Code default |
|---|---|---|---|---|---|---|---|
| Opus 5.5 | Yes | Yes | Yes | Yes | Yes | medium | medium |
| Sonnet 5.5 | Yes | Yes | Yes | Yes | Yes | high | medium |
| Haiku 5.5 | Yes | Yes | Yes | Yes | Yes | medium | medium |
| Opus 5 | Yes | Yes | Yes | Yes | Yes | high | high |
| Fable 5.2 / Mythos 5.2 | Yes | Yes | Yes | Yes | Yes | high | medium (community-reported, not verified in Claude Code docs) |

Mythos 5.2 is available under limited access only. The documentation gives it the same effort recommendations as Fable 5.2.

**Official per-model recommendations (condensed from the "Effort" documentation)**

- **Fable 5.2:** start at `high`. Most of the capability gain is already present at high. Use `max` only to measure the ceiling, since it is slower and costs more than xhigh, often for only a small gain.
- **Opus 5.5:** start at `medium`, then run an effort sweep against your own evals.
- **Sonnet 5.5:** for agentic coding, `medium` on well-specified tasks and `high` on harder or longer ones. Use xhigh or max only where evals show a gain.
- **Haiku 5.5:** `medium` for most work, including agentic coding. `low` for chat and simple tasks, with the caveat that in long agent prompts `low` more often skips a search or a check. `high` for knowledge work and strict instruction following.

## 3\. Usage multipliers: what exists and what does not

Anthropic has not published a table of subscription usage multipliers per effort level. Neither the Help Center, nor the Claude Code documentation, nor the system card gives figures of the form "Low = X×, High = Y×". The "Recommended" and "1.5× or more usage" labels visible in the claude.ai model picker do not appear in any Help Center article; the Help Center calls the recommended level "Default". The "1.5×" label on Max was reported as an iOS app leak (1 Sep 2026, unofficial source).

| Level | `/effort` slider in Claude Code, Opus 5.5 (cost vs medium) | Artificial Analysis, Opus 5.5 (cost per task vs medium) | Status |
|---|---|---|---|
| Low | ~0.7× | ~0.41× ($0.55) | Community / independent measurement |
| Medium | 1× (default) | 1× ($1.34) | — |
| High | ~1.5× | ~1.36× ($1.82) | Community / independent |
| Extra (xhigh) | ~2.5× | ~2.58× ($3.46) | Community / independent |
| Max | ~5.2×, with a warning about excessive tokens and overthinking | ~4.46× ($5.98) | Community / independent |

The slider figures were transcribed from a Claude Code 2.1.318 screen by brainy.ink — community reporting, not official documentation. The Artificial Analysis figures come from Intelligence Index v4.3.2 (measured at launch, 22 Sep 2026), an independent evaluator. The corresponding index scores are 42 / 51 / 54 / 56 / 58. Artificial Analysis notes that Opus 5.5 at max, xhigh, high and medium all sit on the Pareto frontier. The newer Index v5 (27 Oct 2026) reports different numbers: Opus 5.5 scores 35 at low effort and 58 at max, at ten times the cost per task ($0.98 vs $10.04).

**Interpretation (inference, not fact).** The "1.5× or more usage" label on Max in claude.ai is most likely a cautious floor rather than an average. Both independent sources show Max costing 4.5× to 5× Medium. The label is probably computed against High, or against simple queries where thinking is short anyway. On hard tasks the real multiplier will be considerably higher than 1.5×. Artificial Analysis (22 Sep 2026) reports that Opus 5.5 at Max consumes roughly 119k output tokens per Intelligence Index task, most of it thinking. For comparison: Opus 5 (max) roughly 73k, Fable 5.1 (max) roughly 78k, GPT-6 Astra (max) roughly 27k.

## 4\. Benchmarks: where higher effort pays off

**Official figures (Opus 5.5 launch post, 22 Sep 2026)**

- **FrontierCode v1.1:** Medium 54.6%, Max 54.4%. Medium wins. According to third-party descriptions of the system card, the drop above Medium comes from the grader penalising edits outside the task scope. Higher effort means more initiative, which means more scope creep.
- **CursorBench 4.0:** Medium 52.5%, Max 57.8%.
- **Terminal-Bench 4.0:** best result 66.4% at xhigh (measured, per system card summaries, in Claude Code's `--bare` mode), Max 64.8%. That gap sits inside the ±2.6-point standard error. Artificial Analysis independently measures 59.6% on TB 4.0.
- Anthropic states that at default settings Opus 5.5 costs 40% less than Opus 5 on typical workloads.

**Per-level breakdown** (read off the launch-post charts by kingy.ai; not verified directly)

| Benchmark | Low | Medium | High | xhigh | Max |
|---|---|---|---|---|---|
| Terminal-Bench 4.0 (score / $ per attempt) | 38.5% / $1.29 | 57.6% / $2.94 | 64.2% / $3.88 | 66.4% / $7.35 | 64.8% / $11.24 |
| CursorBench 4.0 | 43.7% / $1.17 | 52.5% / $2.91 | 56.0% / $3.97 | 56.0% / $6.98 | 57.8% / $13.43 |
| GDPval-AA (Elo) | 1224 / $0.21 | 1576 / $0.86 | 1692 / $1.54 | 1820 / $4.21 | 1846 / $8.92 |

What follows from this:

- **Low to Medium is the largest quality jump** (TB4 +19 points, GDPval +352 Elo). Low on complex tasks is a false economy.
- **Medium to High:** on TB4, +6.6 points for +32% cost; on CursorBench, +3.5 points for +36%. For agentic and terminal coding this is the best return above Medium.
- **High to xhigh:** on CursorBench, zero points for +76% cost; on TB4, +2.2 points for +89%. Worth it only for long-horizon work.
- **xhigh to Max:** on TB4 the score nominally drops (within the ±2.6-point error); on CursorBench it gains 1.8 points for +92% cost. The Claude Code documentation notes that max may show diminishing returns and is prone to overthinking.

**Where effort measurably helps** (Thariq, Terminal-Bench 3.0, official claude.dev blog)

- Fable 5.1, security: 64% (low) to 87% (top). Hardware: 34% to 75%. Median tokens 73k to 222k.
- `mvcc-lsm-compaction` (fixing a storage-engine bug from a crash report): Opus 5.5 scored 0/5 at low and 4/5 at xhigh. At low, Claude edited code before building and running the reproducer.
- `cli-2ph-simplex` (an LP solver): Opus 5.5 scored 0/5 at low and 5/5 at high.
- A fitness tracker built from a one-sentence prompt: about 1.5 minutes at low versus 67 minutes at max. At max the model made far more decisions on its own.

**Conclusions by task category**

- **Higher effort wins:** hard debugging with a reproducer, storage and concurrency work, security review, numerical and hardware code, long autonomous runs, and ambiguous specs — provided the model has the right approach.
- **Higher effort burns tokens:** boilerplate, renames, formatting, simple edits, lookups, and tasks with one obvious answer. Also tasks where scope creep is penalised (FrontierCode-style "do exactly X"). In a brainy.ink test, renaming a layer at low took about 3 seconds and produced the correct result; Opus at max took 8 seconds and 650 thinking tokens and added an unwanted element.

## 5\. Effort in Claude Code (CLI/TUI)

**How to set it, in descending order of precedence**

| Method | Scope | Notes |
|---|---|---|
| `CLAUDE_CODE_EFFORT_LEVEL=<level>` | As long as it is exported | Overrides everything, including subagent frontmatter. The only way to pin `max` persistently. `auto` or `unset` means send no effort |
| `claude --effort <level>` | One session | Also accepts `ultracode` (starts at xhigh) |
| `/effort` (slider), `/effort high`, `/effort auto`, `/effort status` | Enter saves per model; `s` applies to this session only | `max` is always session-only |
| Slider in `/model` (left/right arrows) | As above | — |
| `modelSettings.<model>.effortLevel` in `~/.claude/settings.json` | Persistent, per model | v2.1.251+. `/effort` writes here itself. Accepts low, medium, high, xhigh — not max |
| Top-level `effortLevel` | Varies | Pitfall: in user settings, Opus 5.5 and newer ignore it. In project, local, managed settings and `--settings` it applies to all models |
| `maxEffortLevel` | A cap, e.g. set by an admin | Higher levels are clamped down to the cap |
| `ultrathink` in the prompt | One turn | Community-reported: since v2.1.311 it may run the turn at xhigh |
| `/effort ultracode` | Session | Not an effort level but a multi-agent orchestration workflow. Consumes limits faster |

**Subagents and effort**

- An `effort` field in subagent or skill frontmatter overrides the session effort, but not the environment variable (official).
- The Agent tool can pass `effort` per invocation, which then overrides frontmatter (v2.1.292, community changelog).
- A subagent with no effort of its own, running on the same model as the session, inherits the session level (official). On a different model it uses that model's saved or default effort (v2.1.317, community).
- `CLAUDE_CODE_SUBAGENT_EFFORT` sets a fallback (v2.1.316, community). `CLAUDE_CODE_SUBAGENT_MODEL` does the same for the model.
- **Model:** `/model opus` also switches subagents that inherit the session model. The documentation warns that switching to Opus before delegating sends research subagents to Opus too. Pin `model:` in the definition to avoid this.

**Plan mode and `opusplan`.** The `opusplan` alias uses Opus in plan mode and Sonnet for execution. That is a model switch, so toggling between modes invalidates the prompt cache (community-reported, consistent with the documented list of invalidators).

**Context management**

- Auto-compaction summarises history as the context approaches `autoCompactWindow`. The window can be set per model and per subagent.
- `/compact` is itself an expensive request, because it reads the whole conversation. Run it before a break, while the cache is still warm, rather than after one.
- `/clear` costs nothing. It resets the context and the session counter.
- Effort does not change compaction mechanics. Higher effort does generate more tokens (thinking, tool results from extra reads and tests), so you reach the compaction threshold sooner (inference).

**claude.ai versus Claude Code**

|  | claude.ai | Claude Code |
|---|---|---|
| Level names | Low, Medium, High, Extra high, Max | low, medium, high, xhigh, max |
| Persistence | A change applies from the next response | Saved per model; max is session-only |
| Thinking toggle | Separate, but unavailable on Haiku, Sonnet, Opus 5.5, Fable 5.1 and Opus 5 (always on) | `/config`; cannot be disabled on 5.5 or Fable |
| Limits | Shared pool with Claude Code | Shared pool with claude.ai |
| Ultracode / ultrathink | Not available | Available |

## 6\. Plans and usage limits (as of November 2026)

| Plan | Price | 5-hour session limit | Weekly limit | Notes |
|---|---|---|---|---|
| Free | $0 | Yes | Yes | Sonnet and Haiku |
| Pro | $20/month | Yes | Yes | Fable only on usage credits |
| Max (formerly "Max 5x") | $100 | 5× Pro every 5 hours | 5× Pro weekly | Separate weekly limit for Fable (up to 50% of the weekly allowance) |
| Ultra (formerly "Max 20x", renamed 30 Oct 2026) | $200 | None for verified accounts | 10× Pro weekly | — |

- **Shared pool.** The Help Center states that limits are shared across Claude and Claude Code, so all activity in both tools counts against the same limits. IDE usage counts too.
- **Pitfall.** If `ANTHROPIC_API_KEY` is set in your environment, Claude Code uses the key instead of the subscription and you pay API rates.
- **Wrap-up allowance.** After hitting the 5-hour limit mid-response, Claude Code may finish the current step ("Usage limit reached · wrapping up"). That counts against the weekly limit.
- **No absolute numbers.** Anthropic does not publish limits in tokens or messages, only relative multipliers. On 22 Sep 2026, at the Opus 5.5 launch, the 5-hour limits were raised without stated figures. Any "$X of API per week" equivalences circulating online are community measurements.

## 7\. API pricing

Per million tokens, official figures.

| Model | Input | Output | 5m cache write | 1h cache write | Cache read | Batch (in/out) | Context / max output |
|---|---|---|---|---|---|---|---|
| Fable 5.2 | $8 | $40 | $10 | $16 | $0.20 (0.025×) | $4 / $20 | 1M / 128K |
| Opus 5.5 | $4 | $20 | $5 | $8 | $0.20 (0.05×) | $2 / $10 | 1M / 128K |
| Sonnet 5.5 | $2 | $10 | $2.50 | $4 | $0.10 (0.05×) | $1 / $5 | 1M |
| Haiku 5.5 (prompt ≤100K) | $0.10 | $0.50 | $0.125 | $0.20 | $0.01 (0.1×) | $0.05 / $0.25 | 1M |
| Haiku 5.5 (prompt >100K) | $0.50 | $2.50 | $0.625 | $1 | $0.05 | $0.25 / $1.25 | — |

- **Asymmetry.** Output costs 5× input on every current model. Thinking is output, so effort hits the most expensive rate.
- **Cache.** Write multipliers are 1.25× (5 minutes) and 2× (1 hour). The 5-minute cache pays for itself after one read, the 1-hour cache after two. These multipliers stack with Batch, long context and data residency (1.1× for US-only inference).
- **1M context.** Opus 5.5 and Sonnet 5.5 have native 1M with no surcharge; the `opus[1m]` alias changes nothing. Haiku 5.5 charges a higher rate above 100K prompt tokens.
- **Fast mode.** Fable 5.2 is $16/$80 (official in launch sources); Opus 5.5 is $8/$40 (third-party).

**Thinking token billing (official)**

- Thinking tokens are billed as output once, at generation time. You pay for the full thinking even when you only see a summary.
- On Opus 4.5+, Sonnet 4.6+, Haiku 5.5 and Fable/Mythos, the API preserves previous thinking blocks by default. They count toward the context window as input, usually cached.
- The older claim that thinking is stripped from subsequent turns no longer holds for these models.

## 8\. Prompt caching in Claude Code

Claude Code caches automatically.

- **TTL.** A subscription on a plan gets a 1-hour TTL on the main conversation; API key, cloud and usage-credit usage default to 5 minutes (community-reported, consistent with the `warm (1h TTL…)` display in `/usage`). Subagents have their own bucket, community-reported as 5 minutes by default.
- **Diagnostics.** `/usage` shows a `Prompt cache (main)` line with hit ratio, miss count and a likely cause (v2.1.260+).

**What invalidates the cache** (from the Claude Code documentation, via summaries)

- Switching models, including toggling `opusplan`
- Changing effort via `/effort`, at least on older models (see the conflict below)
- Turning on fast mode
- Connecting or disconnecting an MCP server whose tools are in the prefix
- A plugin carrying an MCP server
- Adding or removing a deny rule on a bare tool name (`Bash`, `*`)
- `/compact`
- Upgrading Claude Code

**Conflicting sources on effort.** Thariq writes that the newest models respond to effort without breaking the prompt cache in Claude Code, and the API documentation confirms that per-message effort preserves the cache on 5.5 and Fable. Parts of the community report that changing effort invalidates it. The most likely reading: on Opus, Sonnet and Haiku 5.5 and Fable 5.x, changing effort is cache-safe; on older models it is not. This is an inference, not a confirmed fact.

**What does not invalidate it.** Skills, commands, agents, hooks, monitors and themes from plugins. Output styles do not either, since they are delivered as a message.

**CLAUDE.md and ordering.** The cache hierarchy is tools, then system, then messages; a change at one level invalidates that level and everything below it. CLAUDE.md is part of the fixed prefix. So keep anything that changes out of it: timestamps, `git status` dumps, the current TODO, "what I was working on last". Editing CLAUDE.md mid-session costs a rebuild of the project-context layer. Within the file itself, ordering matters less than stability, because the file loads as a whole. That last point is an inference from prefix-cache mechanics; the documentation says nothing about ordering inside CLAUDE.md.

## 9\. The cost of tools, MCP and skills

- **MCP.** Tool definitions are deferred by default. Only tool names and server instructions enter the context until Claude actually uses a specific tool. Older articles claiming 10,000 to 20,000 tokens per server describe versions predating tool search. Even so, the documentation says CLI tools (`gh`, `aws`, `gcloud`, `sentry-cli`) are still more context-efficient. Disable unused servers with `/mcp`.
- **Skills.** Only the name and description enter the context; the full body loads on invocation. This is why the documentation recommends moving workflow-specific instructions out of CLAUDE.md and into skills.
- **Agent teams.** Roughly 7× the tokens of a standard session when teammates run in plan mode. Each teammate loads CLAUDE.md, MCP and skills.
- **Background work.** Summaries for `--resume` and similar typically cost under $0.04 per session. Prompt suggestions are mostly cache reads.
- **Enterprise benchmark (official, Claude Code docs, "Manage costs effectively").** Across enterprise deployments the average cost is around $13 per developer per active day and $150 to $250 per developer per month, with costs staying below $30 per active day for 90% of users.

## Decision matrix: effort per task

| Task | Model | Effort | Why |
|---|---|---|---|
| Rename, formatting, boilerplate, simple edit | Sonnet 5.5 / Haiku 5.5 | low | One answer, and you check the result anyway. Higher effort invites scope creep |
| Lookup, grepping a repo, "where is X" | Haiku 5.5 (subagent) | low | Documentation recommends low for subagents |
| Feature with a clear spec | Opus 5.5 / Sonnet 5.5 | medium | The official sweet spot. FrontierCode scores best at medium |
| Code review, research, summaries | Opus 5.5 | medium | Anthropic's recommendation for Opus 5.5 |
| Bug with edge cases, flaky test, concurrency | Opus 5.5 | high | TB4 gains 6.6 points for +32%. Effort catches edge cases |
| Security review, storage engine, numerical code | Opus 5.5 / Fable 5.2 | high to xhigh | The largest effort deltas in TB3 |
| Long autonomous run (over 30 min), large refactor | Opus 5.5 / Fable 5.2 | xhigh | Documentation recommends xhigh for long-running agentic and coding tasks over 30 minutes |
| A single frontier problem while you step away | Fable 5.2 | max (once, session-only) | Only when xhigh was not enough. Max can score below xhigh |
| Claude knew but did not check (skipped a file or the tests) | Same model | One level up | The diagnosis given in the Claude Code blog |
| Claude tried properly and is still wrong | Larger model | Unchanged | Effort does not fix a wrong approach |
| Mathematics without tools | Opus 5.5 | high or above, or give it a tool | brainy.ink found every level correct with tools, while lower levels hallucinated results without them |

Anthropic's suggested workflow (Thariq): interview for the spec, build at low, review it yourself, verify at high.

## Snippets A: CLAUDE.md modules

Impact key: **High** = large saving, **Medium** = moderate, **Quality** = fewer reruns rather than fewer tokens. Impact estimates are inferences unless marked otherwise.

Each module is a separate block. Combine only what you need. Keep the whole CLAUDE.md under roughly 200 lines (official recommendation) and free of dynamic content.

**A1. `terse` — short replies.** Medium impact. Use always, unless you want essays; effort does not shorten replies but prompts do.

```markdown
## Output
- Answer in the fewest words that are correct. No preamble, no recap of my request.
- After edits: list changed files + one line per change. No full-file echoes.
- Don't explain code unless asked.
```

**A2. `no-wasteful-reads` — frugal file access.** High impact. Use in large repos; full-file reads are the main input drain.

```markdown
## File access
- Locate with Grep/Glob first; Read only the files you will change or must understand.
- Read with offset/limit around the relevant symbol; avoid full reads of files >300 lines.
- Never re-read a file you already read this session unless it changed.
- Never read: lockfiles, dist/, build/, node_modules/, *.min.*, generated code.
```

**A3. `delegate-search` — subagent for exploration.** High impact. Use when exploring unfamiliar code, logs or docs; verbose output stays in the subagent's context and only a summary returns.

```markdown
## Delegation
- For codebase exploration, log analysis, or doc lookups, use the `scout` subagent.
- Ask it for: file paths + line ranges + a ≤10-line summary. Do not paste its raw findings.
```

**A4. `just-do-it` — fewer questions, fewer round trips.** Medium impact. Use on well-specified tasks; every question costs another turn carrying the full context.

```markdown
## Autonomy
- If the request is unambiguous, act; don't ask for confirmation.
- If ambiguous, make the most reasonable assumption, state it in one line, proceed.
- Only stop to ask when a wrong guess would be destructive or expensive to undo.
```

**A5. `test-before-done` — verification.** Quality impact. Use always for code; the documentation advises giving verification targets, and this is cheaper than corrections in later turns.

```markdown
## Done means verified
- Before saying "done": run the narrowest relevant test/lint/typecheck.
- Report only failures (first 30 lines). If no test exists, write a minimal one.
```

**A6. `compact-instructions` — what to keep when compacting.** Medium impact. This is the official pattern from the documentation.

```markdown
# Compact instructions
When compacting, keep: current task goal, decisions made, files changed, failing tests.
Drop: raw tool output, exploration dead-ends, full file contents.
```

**A7. `index-not-encyclopedia` — CLAUDE.md as an index.** High impact. Use when you have a lot of project documentation; skills load on demand. Note that `@imports` load at startup, so they save nothing by themselves.

```markdown
## Where things are (load only when relevant)
- DB migrations workflow → skill `db-migrate`
- Release process → skill `release`
- Architecture overview → skill `codebase-overview`
```

## Snippets B: settings.json and settings.local.json

**B1. Per-model effort, persistent, in user settings.** Quality to high impact. Use when you want Opus on medium and Sonnet on low. Note that top-level `effortLevel` in user settings is ignored by Opus 5.5.

```json
{
  "model": "opus",
  "modelSettings": {
    "claude-opus-5-5": { "effortLevel": "medium" },
    "claude-sonnet-5-5": { "effortLevel": "low" },
    "claude-fable-5-2": { "effortLevel": "high", "maxEffortLevel": "xhigh" }
  }
}
```

`maxEffortLevel` as a field inside `modelSettings` is documented. The value `max` is not accepted in `effortLevel`.

**B2. Effort for a whole project** (`.claude/settings.json`, committed). Use when a team should work at one level. In project settings, top-level `effortLevel` applies to all models.

```json
{ "effortLevel": "medium" }
```

**B3. A cheap default model for subagents.** High impact. Use when the main session runs Opus and you do not want every generic subagent on Opus too.

```json
{
  "env": {
    "CLAUDE_CODE_SUBAGENT_MODEL": "haiku",
    "CLAUDE_CODE_SUBAGENT_EFFORT": "low"
  }
}
```

`CLAUDE_CODE_SUBAGENT_EFFORT` comes from the v2.1.316 changelog (community source). Check `claude --version`.

**B4. Permissions: block expensive reads.** High impact. Use always, and set it at startup rather than mid-session. A deny on a bare tool name (for example `"Bash"`) invalidates the cache; path rules are a separate category.

```json
{
  "permissions": {
    "deny": [
      "Read(./node_modules/**)", "Read(./dist/**)", "Read(./build/**)",
      "Read(./**/*.lock)", "Read(./**/*.min.js)", "Read(./.env*)"
    ],
    "allow": ["Bash(npm run lint)", "Bash(npm test:*)"]
  }
}
```

**B5. Hook: filter test output.** High impact; this is the official example from the documentation. Use when tests emit thousands of lines — the documentation describes going from tens of thousands of tokens to hundreds.

```json
{
  "hooks": {
    "PreToolUse": [
      { "matcher": "Bash",
        "hooks": [{ "type": "command", "command": "~/.claude/hooks/filter-test-output.sh" }] }
    ]
  }
}
```

```bash
#!/bin/bash
# ~/.claude/hooks/filter-test-output.sh  (chmod +x)
input=$(cat)
cmd=$(echo "$input" | jq -r '.tool_input.command')
pattern='^(npm test|pytest|go test)( [^;&|<>`$#\\[:cntrl:]]*)?$'
if [[ "$cmd" =~ $pattern ]]; then
  filtered_cmd="$cmd 2>&1 | grep -A 5 -E '(FAIL|ERROR|error:)' | head -100"
  echo "$input" | jq --arg filtered "$filtered_cmd" \
    '{hookSpecificOutput: {hookEventName: "PreToolUse", updatedInput: (.tool_input + {command: $filtered})}}'
else
  echo "{}"
fi
```

**B6. Statusline: show effort and cache.** Quality impact. Use when you want to see which level you are actually on and whether the cache is warm. The statusline JSON carries `effort.level` and a `prompt_cache` object.

```json
{ "statusLine": { "type": "command", "command": "~/.claude/statusline.sh" } }
```

```bash
#!/bin/bash
# ~/.claude/statusline.sh
input=$(cat)
MODEL=$(echo "$input" | jq -r '.model.display_name // "?"')
EFFORT=$(echo "$input" | jq -r '.effort.level // "default"')
echo "$MODEL ($EFFORT)"
```

`effort.level` is the session level, not a level set per turn by a skill, a subagent or ultrathink. Check the field names inside `prompt_cache` against the statusline documentation; they are unverified here.

**B7. Persistent `max`, only if you truly need it.**

```bash
# ~/.zshrc — the only way to pin max; it also overrides subagent frontmatter
export CLAUDE_CODE_EFFORT_LEVEL=max
```

This is an anti-pattern for most people. It overrides the effort of every subagent, so cheap `scout` agents run at max too.

## Snippets C: slash commands and skills

Skills and commands support a `model` frontmatter field. Skills also support `effort` (official). Commands are assumed to as well, but that is worth verifying.

**C1. `/quick` — a fast, cheap change.** High impact.

```markdown
---
description: Small mechanical change, no exploration
model: sonnet
effort: low
---
Make exactly this change, nothing else: $ARGUMENTS
Touch only the files strictly required. No refactors, no extra tests unless one breaks.
Reply with changed files only.
```

**C2. `/deepfix` — a hard bug.** Quality impact.

```markdown
---
description: Hard bug with reproducer, edge-case hunting
model: opus
effort: high
---
Bug: $ARGUMENTS
1. Build and run the reproducer BEFORE editing.
2. Write a test that fails on the current code.
3. Fix. Confirm the new test would have caught the original bug.
4. Check adjacent edge cases (empty, concurrent, boundary). Report only what you changed and why.
```

**C3. `/handoff` — run before `/clear`.** High impact. Use when you are finishing a thread and want a clean start without losing state: `/clear` costs nothing, while `/compact` on a large context is expensive.

```markdown
---
description: Write a handoff note so I can /clear
model: haiku
effort: low
---
Write HANDOFF.md (≤25 lines): goal, decisions, files touched, open issues, next step.
No code blocks longer than 5 lines.
```

Then run `/clear` and say "read HANDOFF.md and continue".

## Snippets D: subagents, model and effort

**D1. `scout` — cheap exploration.** High impact.

```markdown
---
name: scout
description: Use PROACTIVELY for codebase search, "where is X", log/doc lookups. Read-only.
tools: Read, Grep, Glob
model: haiku
effort: low
maxTurns: 15
omitClaudeMd: true
---
Find what was asked. Return ONLY: file paths with line ranges + ≤10-line summary.
Never paste whole files. Stop as soon as you have enough.
```

`omitClaudeMd` appears in the documented list of frontmatter fields. Its exact semantics are unverified here, but the name suggests it skips loading CLAUDE.md.

**D2. `reviewer` — review without editing.**

```markdown
---
name: reviewer
description: Review a diff for bugs and risky changes after code is written.
tools: Read, Grep, Glob, Bash
model: opus
effort: medium
---
Review only the changed lines and their direct callers. Report real bugs first, nits last, max 10 items.
```

**D3. `deep-debugger` — reserved for hard problems.**

```markdown
---
name: deep-debugger
description: Use only for bugs that survived one fix attempt, concurrency/storage/security issues.
model: opus
effort: xhigh
---
Reproduce first. Hypothesize ≥2 root causes, falsify each with a test. Fix the confirmed one.
```

**D4. `test-runner` — isolate verbose output.** High impact.

```markdown
---
name: test-runner
description: Run test suites and report failures only.
tools: Bash, Read
model: haiku
effort: low
---
Run the requested tests. Return: pass/fail counts + first failing assertion per test (≤5 lines each).
```

## Snippets E: prompt patterns

**E1. Low-effort prompt — narrow and concrete.**

```text
In src/auth/login.ts, add input validation to validateCredentials():
reject empty email/password, return {ok:false, error}. Don't touch other files.
```

The documentation notes that specific requests let Claude work efficiently with minimal file reads. Instead of "go understand the codebase", point at files with `@path`.

**E2. High-effort prompt — outcome plus verification criteria.**

```text
Goal: compaction must not lose writes under concurrent flush.
Repro: `make repro-1432` (currently fails).
Done when: repro passes, new regression test fails on old code, full storage suite green.
Investigate before editing. Report root cause in ≤5 lines.
```

**E3. Keeping low effort on a hard step.** This is the official pattern from the Opus 4.7 documentation; it appears transferable.

```text
This task involves multistep reasoning. Think carefully before responding.
```

## Stacks: how to combine the modules

| Stack | Main | Subagents | CLAUDE.md | Settings | When |
|---|---|---|---|---|---|
| Cheap exploration | Sonnet 5.5 at low | `scout` (Haiku/low), `test-runner` | A1 + A2 + A3 | B3 + B4 + B5 | Onboarding into a repo, Q&A about code, small changes |
| Daily driver | Opus 5.5 at medium | `scout`, `reviewer` (Opus/medium) | A1 through A6 | B1 + B4 + B5 + B6 | 80% of feature work |
| Heavy refactor | Opus 5.5 at high, starting in plan mode | `scout`, `test-runner`, `deep-debugger` on demand | A2 + A5 + A6 + A7 | B1 + B4 + B5 | Multi-file changes. Plan, approve, execute. `/handoff` plus `/clear` between phases |
| Long autonomous run | Opus 5.5 or Fable 5.2 at xhigh, optionally ultracode | All with `model` and `effort` pinned | A4 + A5 + A6 | B1 (with `maxEffortLevel`) | Runs over 30 minutes. Large `max_tokens`. Set it at the start and do not change it mid-run |
| Research and docs | Opus 5.5 at medium | `scout` on Haiku for fetch and grep | A1 + A3 | CLI over MCP; `/mcp` off for unused servers | Synthesis in the main session, collection in subagents |
| API batch | Haiku or Sonnet 5.5 at low, Batch API (−50%), 1-hour cache on a shared prefix | — | — | — | Classification, extraction, overnight jobs |

## Recommendations

1. **Stay on Medium as the default for Opus 5.5.** Raise to High per task (`/effort high`, run the task, `/effort auto`) when Claude skipped a file, the tests or an edge case. On 5.5 and Fable this change is probably cache-safe; on older models make it at the start of a thread.
2. **Do not pin Max.** On TB4 it scores nominally below xhigh (within the error bar), and in AA Index v4.3.2 it cost about 4.5× Medium, while the Claude Code slider estimates around 5.2×. Read the claude.ai "1.5× or more usage" label as a floor.
3. **Pin `model` and `effort` in every custom subagent.** Otherwise switching the main session to Opus or max drags the whole fleet with it.
4. **Choose model and effort at the start, then leave them alone.** Do not connect MCP servers mid-task, and run `/compact` before a break. This is the single largest invisible source of waste.
5. **Run `/usage` once a week.** On Pro, Max, Team and Enterprise plans it attributes usage to skills, subagents and MCP and offers recommendations; pressing `f` applies some of them automatically (v2.1.305+).
6. **Check that `ANTHROPIC_API_KEY` is not set in your environment** if you are paying for a subscription.

## Known anti-patterns that burn tokens

- One session running all day without `/clear`. Every turn resends the entire history.
- `/model` or an effort change mid-task on older models, causing a cache miss on the full context.
- Returning after more than 5 minutes (API) or more than 1 hour (subagents) and immediately running `/compact`. That reprocesses the whole history at full price.
- CLAUDE.md as an encyclopedia, with dynamic entries, or edited mid-session.
- "Fix this codebase" instead of naming a specific file and function.
- Subagents with no `model:` inheriting Opus, and `CLAUDE_CODE_EFFORT_LEVEL=max` left in the shell.
- Agent teams for small tasks (roughly 7× the tokens), and teammates left running after the work is done.
- `/loop` and scheduled tasks in an idle session. Each firing sends the full context.
- Unfiltered tests and logs landing in the context in full.
- Carrying Opus 5 effort settings over to Opus 5.5. The same level means a different amount of thinking (5.5 does more per turn at xhigh and max).

## Caveats

- **No official per-level usage multipliers exist.** Every "N× usage" figure in this document comes either from the UI, transcribed by the community, or from Artificial Analysis. Do not treat them as an SLA.
- **The per-level benchmark breakdown** (TB4, CursorBench, GDPval) was read off the launch-post charts by a third party. The numbers stated officially in the text are only: FrontierCode medium 54.6% and max 54.4%, CursorBench medium 52.5% and max 57.8%, TB4 xhigh 66.4% and max 64.8%. Section 8 of the system card (the SWE-bench Pro effort sweep) could not be verified. The circulating claim of "SWE-bench Pro subset: −2.5 points at medium for 70% of high's cost" should be treated as unconfirmed.
- **Claude Code versions tied to subagent-effort features** (v2.1.292, 316, 317; ultrathink to xhigh in v2.1.311) come from community changelog summaries. Check `claude --version` and the official changelog.
- **The Fable 5.2 default in Claude Code and the apps** (medium) is third-party information. On the API it is `high`, officially.
- **Picker labels** ("High — Recommended", "Max — 1.5× or more usage") may vary by model and platform. The Help Center describes the marker as "Default".
- Fable 5.2 was released on 9 Nov 2026, one day before the date of this document. Parts of its characterisation may still change in the documentation.

## Sources

**Official — API and model documentation**

- [Effort](https://platform.claude.com/docs/en/build-with-claude/effort) — levels, per-model defaults and recommendations
- [Adaptive thinking](https://platform.claude.com/docs/en/build-with-claude/adaptive-thinking) and [Extended thinking](https://platform.claude.com/docs/en/build-with-claude/thinking) — thinking support and billing
- [Thinking, steering and cost](https://platform.claude.com/docs/en/build-with-claude/thinking-steering-and-cost)
- [Prompt caching](https://platform.claude.com/docs/en/build-with-claude/prompt-caching) — TTLs and write/read multipliers
- [Pricing](https://platform.claude.com/docs/en/about-claude/pricing) and [Context windows](https://platform.claude.com/docs/en/build-with-claude/context-windows)
- [What's new in Opus 5.5](https://platform.claude.com/docs/en/models/opus-5-5/whats-new-opus-5-5) and [Prompting Opus 5.5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5-5)
- [Haiku 5.5 overview](https://platform.claude.com/docs/en/models/haiku-5-5/overview) and [Fable 5.2 overview](https://platform.claude.com/docs/en/models/fable-5-2/overview)

**Official — Claude Code**

- [Model configuration](https://code.claude.com/docs/en/model-config) — `/effort`, env vars, precedence
- [Manage costs effectively](https://code.claude.com/docs/en/costs) — the enterprise cost benchmark
- [Settings reference](https://code.claude.com/docs/en/settings-reference) and [Subagents](https://code.claude.com/docs/en/sub-agents)
- [Prompt caching in Claude Code](https://code.claude.com/docs/en/prompt-caching) — what invalidates the cache
- [Claude model and effort level in Claude Code](https://claude.com/blog/claude-model-and-effort-level-in-claude-code) — Lydia Hallie, 7 Jul 2026

**Official — launch and support**

- [Claude Opus 5.5](https://www.anthropic.com/claude-opus-5-5) — launch post and benchmark figures
- [Change the model, effort and thinking settings](https://support.claude.com/en/articles/8664678-change-the-model-effort-and-thinking-settings)
- [How usage and length limits work](https://support.claude.com/en/articles/11647753-how-do-usage-and-length-limits-work)
- [Using Claude Code with a Pro or Max plan](https://support.claude.com/en/articles/11145838-use-claude-code-with-your-pro-or-max-plan) and [What is the Max plan](https://support.claude.com/en/articles/11049741-what-is-the-max-plan)

**Third-party and community** — treat as unverified

- [Spending your effort](https://pub.towardsai.net/using-claude-code-spending-your-effort-cadb1577339f) — Thariq Shihipar, 25 Sep 2026; the Terminal-Bench 3.0 effort sweeps
- [Claude effort levels](https://brainy.ink/paper/claude-effort-levels) — the `/effort` slider cost estimates and hands-on tests
- [Opus 5.5 specs, benchmarks, pricing](https://kingy.ai/blog/claude-opus-5-5-specs-benchmarks-pricing-comparison/) — the per-level benchmark table read off the launch charts
- [iOS app usage warning on max effort](https://www.progressiverobot.com/2026/09/01/claude-ios-app-usage-warning-max-effort-mode/) — the reported source of the "1.5×" label

Artificial Analysis Intelligence Index v4.3.2 (22 Sep 2026) and v5 (27 Oct 2026) figures are quoted as reported in the sources above.
