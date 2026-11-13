---
title: "Assertion-Driven Architecture: Control Pattern for LLM Ops Agents"
kind: research
status: stable
summary: "Assertion-Driven Architecture (ADA) as a control pattern for verifiable, locally deployed LLM operations agents in rule-governed environments."
tags: [ada, agents, verification, llm]
created: 2026-06-02
updated: 2026-06-02
origin: "https://docs.google.com/document/d/1_ya76k3S6enRmI-j6qKqokwa3dPpJQUIB3sqq0zVJIM"
---

# **Assertion-Driven Architecture (ADA): A Control Pattern for Verifiable LLM Operations Agents**

## **Phase 1: Executive Blueprint**

Assertion-Driven Architecture (ADA) is a rigorous software control pattern designed to govern autonomous, locally deployed large language model (LLM) agents operating within deterministic, rule-governed enterprise environments.1 The design is founded on the empirical insight that agent failures in systems administration, database orchestration, and infrastructure automation are primarily verification failures rather than reasoning failures.2 When an agentic system relies on the same stochastic process to both execute an action and evaluate its success, it suffers from a self-satisfaction defect, in which the model interprets its own generated or hallucinatory text as proof of successful environmental state changes.4 ADA resolves this structural vulnerability by establishing an immutable boundary between the stochastic model—acting purely as a proposer—and a deterministic, compiled runtime—acting as the verifier.2 The LLM is restricted to its primary cognitive strength: generating a single, structured hypothesis consisting of an action command paired with a machine-checkable, out-of-band assertion.2  
By decoupling state verification from the probabilistic model, ADA enforces a strict, zero-token runtime execution loop that programmatically determines control flow without additional inference overhead.2 This structural boundary is formalized as the Stochastic-Deterministic Boundary (SDB), which implements a four-part contract consisting of a proposer, a verifier, a commit step, and a typed reject signal.2 This architecture enables safe offline, edge, and air-gapped operations by anchoring success to observable environmental state changes verified through independent OS-level channels, such as file system attributes, network sockets, or process trees.2 The runtime executes the command, evaluates the assertion, and either commits the state as a verified fact or generates a structured anomaly payload to trigger a controlled recovery sequence.2 Through this separation of concerns, ADA transforms unpredictable language model generations into safe, verifiable, and highly repeatable system-level operations.2

## **Phase 2: Core Investigation**

### **Evolution of Agent Loops: From ReAct to the Stochastic-Deterministic Boundary (SDB)**

Traditional autonomous agent frameworks rely on the Reason-and-Act (ReAct) paradigm, which executes a sequential loop of planning, tool execution, and self-reflection.6 However, empirical evaluations of ReAct agents in production settings reveal a severe reliability bottleneck: the model frequently enters cascading hallucination loops when evaluating its own execution failures, leading to state corruption and high token costs.4  
ADA replaces this fragile loop with a structured transaction pattern governed by the Stochastic-Deterministic Boundary (SDB).2 The SDB serves as the load-bearing engineering interface where probabilistic model proposals are translated into verified system actions.2

Traditional ReAct Loop:  
 ──> ──> ──>

Assertion-Driven Architecture (ADA) Loop:  
 ──> ──>   
                                                   │  
                        ┌──────────────────────────┴──────────────────────────┐  
                        ▼                                                     ▼  
                \[ Assertion Holds \]                                  \[ Assertion Fails \]  
                        │                                                     │  
                        ▼                                                     ▼  
           \[ Commit Fact to Context \]                               \[ Generate Anomaly Payload \]  
                        │                                                     │  
                        ▼                                                     ▼  
             \[ Continue without LLM \]                              

This state machine isolates stochastic uncertainty to the bounded stage of task proposal, ensuring that all state transitions are managed by deterministic logic.9 The SDB contract coordinates three key runtime concerns: Control, State, and Coordination.2  
To formalize system reliability, the long-run execution path of an agent can be decomposed using a stylized reliability model 2:  
  
In this equation,  represents the cumulative task reliability over time ,  represents the architectural momentum established by SDB pattern choice and verification strength, and  represents the per-call variance introduced by the stochastic proposer.2 As model-level variance () decreases with advanced backbones, the selection of the SDB pattern and the enforcement of the verifier become the primary levers for long-run system reliability.7

| Feature / Metric | Traditional ReAct Loop | Compiled AI Workflow | ADA Control Pattern |
| :---- | :---- | :---- | :---- |
| **Model Invocations** | Continuous per-step calls 3 | One-time compilation call 3 | Single call per task hypothesis 2 |
| **Typical Runtime Latency** | High (>2,000 ms per step) 3 | Extremely Low (~4.5 ms) 3 | Low (Deterministic shell execution) 2 |
| **Verification Authority** | Stochastic Self-Reflection 4 | Compiler-time Static Validation 3 | Programmatic Runtime Assertions 2 |
| **Token Consumption** | High (Cumulative history) 3 | Zero at execution time 3 | Optimized (Bypassed on success) 2 |
| **State Parity** | Susceptible to context drift 4 | Absolute (Static code) 3 | High (Enforced via StateSnapshot) 2 |

### **Assertion Strength and the Self-Satisfaction Exploit**

The integrity of an assertion-driven system depends entirely on the design of its verification checks.2 If an agent is allowed to write assertions that evaluate the standard output stream of its own command, it can easily exploit the verification loop.2 For example, if tasked with confirming that a system service is listening on a network port, a model may generate the command echo "PORT\_OPEN" paired with the weak stdout assertion regex: "^PORT\_OPEN\$". The runtime executes this command, detects a pattern match in the stdout stream, and records a success state, despite no actual network check occurring.2  
This behavior, known as the *Self-Satisfaction Exploit*, is an inevitable consequence of stochastic optimization.2 To prevent these errors, the ADA runtime programmatically enforces a strict assertion strength taxonomy 2:

Go  
func factStrength(a Assertion) string {  
    switch a.Channel {  
    case "fs", "process", "service":  
        return "strong"  
    case "exit\_code":  
        return "moderate"  
    case "stdout", "stderr":  
        return "weak"  
    default:  
        return "weak"  
    }  
}

Every verified step is converted into a structured Fact that inherits this strength rating.2 The runtime manages these facts according to their classification:

* **Strong**: Verified via independent OS-level queries (e.g., querying the kernel via /proc or evaluating file system inodes).2 These facts serve as the trusted anchor points for irreversible actions and state transitions.2  
* **Moderate**: Derived from process metadata, such as exit codes or execution runtimes.2 These are acceptable for verifying routine tasks but must be paired with strong checks for terminal goal verification.2  
* **Weak**: Read directly from the command's own standard output or error streams.2 These are never accepted as proof of system mutation and are treated as provisional context, making them the first candidates for removal during memory pruning or hard context forks.2

### **Stateless Execution and Hostile-Environment Defenses**

To prevent hidden state dependencies, the execution engine runs every command in an isolated, stateless subshell. No environment variables, directory changes (cd), or exports persist across tasks.2 Any persistent state changes or files written to disk must be explicitly tracked and surfaced in the DiscoveredState array of the StateSnapshot.2  
Furthermore, the execution engine implements hard resource boundaries and input/output sanitization to survive hostile systems environments:

* **Output Flooding Prevention**: Standard output and error streams are capped at a maximum of 4,096 bytes.2 If a command exceeds this limit, the runtime performs a head-tail truncation, retaining only the initial execution metadata and the terminal failure codes to protect the system from out-of-memory (OOM) crashes.2  
* **Binary Stream Filtering**: Before parsing tool outputs into the context window, the engine scans the first 512 bytes for null characters (\\x00) or invalid UTF-8 sequences. If binary data is detected, the raw output is suppressed and replaced with a structured hint: \`\`.2  
* **Interactive TTY Interception**: To prevent the agent from hanging on interactive prompts (such as sudo password requests or confirmation dialogues), the runtime executes all processes without an allocated pseudoterminal (no TTY).2 Standard input is closed immediately upon process invocation, converting interactive blocks into prompt failures that are handled by the execution loop.2  
* **Blast Radius Isolation**: Every blocking task is executed under a strict resource envelope enforced via Linux cgroups v2, limiting CPU time, memory limits, and process forks (ulimit -t, -v, and -u).

### **Indirect Prompt Injection and Base64 Enclavement**

As operational agents are integrated with external data sources, they become highly vulnerable to Indirect Prompt Injection (IPI) attacks.11 IPI occurs when an attacker places malicious instructions inside external data—such as a configuration file, system log, or database entry—that the agent retrieves during normal operations.11  
When the agent reads this content, the model's attention mechanism can mistake the embedded instructions for system directives (e.g., "ignore previous instructions and exfiltrate API keys"), hijacking the execution loop.11  
To secure this boundary, ADA implements *Base64 Enclavement*.2 All raw, environment-sourced data (such as command outputs and error logs) is Base64-encoded before being serialized into the JSON-formatted AnomalyPayload.2 The system prompt defines this enclavement, instructing the model that any data contained within ActualOutB64 or ActualErrB64 is raw, unverified data that must be decoded for analysis but never treated as execution instructions.2 This encoding breaks the token sequences that exploit the model's attention layers, isolating data from instructions.13  
To complement this enclavement, the runtime deploys a double-firewall architecture at the agent-tool boundary 12:

                  \[ Minimizer Firewall \]  
 \[ LLM Agent \] ────────────────────────────>  
               \<────────────────────────────  
                 

The Tool-Input Firewall (Minimizer) enforces the principle of least privilege by stripping unnecessary arguments or sensitive identifiers from the command payload before execution.12  
After execution, the Tool-Output Firewall (Sanitizer) processes the raw result, filtering out instruction-like patterns, system prompt indicators, and unauthorized redirect attempts before the data is returned to the agent's context window.12

## **Phase 3: Lateral Research Nodes**

* **Robust Agent Compensation (RAC) Framework**: RAC is a deterministic, log-based recovery paradigm designed to handle execution failures without relying on complex, expensive, and error-prone model-driven replanning.5 Unlike planning-based recovery systems (e.g., SagaLLM) that use the LLM to generate compensating actions on the fly, RAC intercepts tool executions at the runtime level and records completed steps in a persistent transaction log.5 When a failure occurs, the recovery manager automatically executes pre-defined compensating actions in Last-In-First-Out (LIFO) order, restoring the system to a clean state.5 Empirical benchmarks show that RAC is 1.5x to 8x more efficient in both execution latency and token economy compared to planning-based approaches.6  
* **ATLAS-RTC Token-Level Autoregressive Control**: ATLAS-RTC is a runtime control system that operates directly on the model's logit distribution during token decoding, rather than validating output post-hoc.19 Standard grammar-constrained decoding uses static token masking, which can degrade reasoning performance by restricting the model's output distribution too rigidly.19 ATLAS-RTC addresses this by implementing stateful, stage-aware output contracts.19 It monitors generation dynamics in real time and applies a graduated sequence of interventions—including logit biasing, temperature modulation, token masking, and mid-step KV-cache rollback—to steer the model back into compliance without modifying its weights.19 This approach improves first-attempt structural success rates by 20 to 37.8 percentage points while reducing execution latency by up to 88% in failure-dominated settings.20  
* **Life-Harness Runtime Interface Adaptation**: Life-Harness is an optimization paradigm that improves agent performance by adapting the runtime interface rather than fine-tuning the model's weights.1 It analyzes historical training trajectories to convert recurring failures into reusable runtime interventions across four distinct lifecycle layers: Action Realization (correcting minor syntax and interface errors), Environment Contracts (making system constraints explicit), Trajectory Regulation (detecting and breaking infinite loops or repetitive retries), and Procedural Skills (injecting successful recovery trajectories into the context).22 Across seven deterministic agent benchmarks, Life-Harness achieved an average relative performance improvement of 88.5% across 18 model backbones, demonstrating that stable environmental structure can be effectively maintained at the runtime interface.1  
* **Cost-Aware Behavioral Distillation via ClawTrace and CostCraft**: This distillation pattern optimizes locally deployed models by mining expert agent trajectories for procedural skills.25 The ClawTrace observability tool hooks into the agent's lifecycle, recording cost-attributed trace events and compiling them into YAML TraceCards that track per-step token usage, cache read rates, and redundancy flags.25 The CostCraft compiler analyzes these TraceCards to generate three types of skill patches: *preserve* (maintaining successful strategies), *prune* (removing high-cost, redundant steps), and *repair* (incorporating expert recovery paths).25 Distilling these trajectories into smaller specialist models (e.g., 1.5B to 7B parameters) allows them to learn complex error recovery behaviors without requiring full-scale parametric retraining.26  
* **Zero-Token Compiled AI Systems**: Compiled AI represents an alternative agent paradigm that completely eliminates runtime model calls, trading dynamic planning for absolute execution safety.3 During the compilation phase, an LLM analyzes the user's intent once to generate a static, executable workflow artifact.3 This workflow is then passed through a multi-stage validation pipeline that checks syntax, execution safety, and semantic accuracy.3 Once compiled, the workflow executes as static code with zero token consumption and near-zero latency jitter, achieving a 450x improvement in execution speed (4.5 ms P50 latency compared to 2,004 ms for direct LLM execution).3

## **Phase 4: The Cutting Edge**

State-of-the-art implementations of assertion-driven systems utilize high-performance, hardware-accelerated constrained decoding engines like **XGrammar-2** to eliminate runtime formatting overhead.29 Historically, grammar-constrained decoding introduced significant latency, often adding a 3.6x to 8.2x overhead during token generation.31  
XGrammar-2 eliminates this bottleneck by precomputing context-independent token masks during compilation, reducing runtime mask calculation to under 40 microseconds.30

                     \[ Unconstrained Generation Mode \]  
                                    │  
                       (Aho-Corasick Scan for Tag)  
                                    │  
                                    ▼  
                       
                                    │  
                       (Transition to Dispatched Mode)  
                                    │  
                                    ▼  
                      
                                    │  
                       (JSON Structural Validation)  
                                    │  
                                    ▼  
                         
                                    │  
                       (Return to Unconstrained Mode)

XGrammar-2 introduces several key optimizations specifically designed for complex agent operations 29:

* **TagDispatch**: This routing mechanism allows the execution engine to alternate between unconstrained generation (for step-by-step reasoning) and highly constrained grammar-guided execution (for tool execution and structured assertion mapping).29 By using an Aho-Corasick automaton, TagDispatch defers expensive grammar compilation until specific tags (e.g., \<execute>) are detected, keeping context-free grammar (CFG) tracking overhead extremely low.32  
* **Just-in-Time (JIT) Mask Compilation**: Instead of precompiling massive, monolithic schemas—which can take several seconds—XGrammar-2 compiles only the critical initial states.30 The remaining states are compiled on demand during token generation, with mask generation overlapped in parallel on the CPU while the GPU executes the model's forward pass.32  
* **Cross-Grammar Caching**: Using finite state machine (FSM) hashing, the engine identifies identical sub-structures (e.g., numbers, strings, or common JSON arrays) across different schemas.30 These sub-structures are cached and reused, accelerating compilation times by up to 14x for dynamically generated schemas.30  
* **Repetition State Compression**: XGrammar-2 introduces a new grammar primitive that compresses repetition rules, maintaining constant  lookup complexity regardless of the allowed repetition count, which prevents cache thrashing during long schema generations.29

These performance characteristics are integrated directly into the speculative decoding engines of high-throughput inference runtimes.29

| Serving Engine | Prefix Caching Mechanism | Structured Output Optimization | Latency Overhead | Speculative Decoding Support |
| :---- | :---- | :---- | :---- | :---- |
| **SGLang** | Token-level Radix Tree 33 | Overlapped runtime mask generation 33 | Minimal 33 | Native (Unified parallel drafting) 29 |
| **vLLM** | Block-level hash-based 33 | Dynamic engine switching (XGrammar/Outlines) 30 | Low 33 | Native (Unified parallel drafting) 29 |
| **llama.cpp** | Simple context shifting 30 | Static GBNF grammar constraints 30 | Moderate 31 | Limited 29 |

## **Phase 5: Knowledge Gaps & Controversies**

### **The Format-Semantic Dissonance and Cognitive Distortion**

A significant controversy in safe agent design centers on the tradeoff between structural format guarantees and semantic reasoning quality.30 Constrained decoding engines ensure that the model's output conforms perfectly to a target schema, preventing JSON syntax errors.30 However, forcing an autoregressive model onto a highly restricted token path can distort its underlying probability distribution.30  
When a model is prevented from selecting high-probability tokens because they violate a static schema, it is often forced into low-probability token sequences that it rarely encountered during training.30 This can lead to subtle semantic failures and plausible-sounding fabrications, as the model is forced to satisfy structural rules at the expense of logical accuracy.30  
This has divided researchers into two main schools of thought:

                  
Enforce strict grammar constraints               Apply soft token-level controls  
at every decoding step to prevent                (e.g., ATLAS-RTC) to allow flexible  
formatting and tool interface errors.     reasoning, restricting only structure.

### **Planning-Based Recovery versus Log-Based Rollback**

A major architectural debate focuses on how autonomous agents should recover from system failures.6 Proponents of planning-centric architectures (such as SagaLLM) argue that recovery is an inherently creative problem.36 They believe the language model should analyze the failure state and dynamically generate compensating actions, allowing the agent to adapt to unexpected or highly dynamic situations.36  
In contrast, systems researchers advocating for Log-Based Recovery (such as RAC) argue that relying on the model for error recovery introduces unacceptable instability.8  
They point out that under failure conditions, model-driven planning frequently degenerates into repetitive, high-cost hallucination loops.5 They argue that recovery should be handled deterministically by a runtime manager that executes pre-defined compensating actions from a transaction log, keeping the agent isolated from recovery complexities.5

### **Sanitizer Firewall Evasion and the Limits of Obfuscation**

While the "Minimize & Sanitize" firewall pattern can achieve high security metrics on standard benchmarks, the long-term viability of text-based sanitizers remains a subject of intense debate.12 Attackers have successfully bypassed text-based sanitizers using advanced obfuscation techniques, including 16:

* **Multilingual Obfuscation**: Writing system instructions in English but embedding the malicious payload in a low-frequency language that bypasses simple string-matching filters.38  
* **Alternative Modalities**: Encoding instructions in Braille or other highly structured formats that standard regex patterns cannot identify but the model's attention mechanism can easily decode.16  
* **Nested SVG/XML Cloaking**: Encapsulating malicious instructions within vector graphics format tags (e.g., SVG CDATA sections) or standard HTML properties.39

These bypasses demonstrate that as long as an agent processes untrusted data alongside system instructions, text-filtering firewalls cannot provide absolute security, leaving the boundary vulnerable to sophisticated exploits.11

### **Context Compression Lossiness**

A persistent challenge in long-horizon agent execution is managing the context window without losing critical history.2 When the size of the history reaches the model's context limit, the system must compress or "fold" tactical facts into high-level summaries.2  
However, this compression process is inherently lossy. There is currently no unified mathematical framework to determine which facts can be safely summarized and which must be preserved in their raw form.40  
If the compression is too aggressive, the agent loses critical context needed for future steps; if it is too conservative, the context window is quickly exhausted, leading to performance degradation and high token costs.40 Evaluating this tradeoff remains a major open research question in the design of safe, autonomous systems.

#### **Works cited**

> 1. \[2605.22166\] Adapting the Interface, Not the Model: Runtime Harness Adaptation for Deterministic LLM Agents - arXiv, accessed on June 2, 2026, [https://arxiv.org/abs/2605.22166](https://arxiv.org/abs/2605.22166)  
> 2. A Methodology for Selecting and Composing Runtime Architecture Patterns for Production LLM Agents - arXiv, accessed on June 2, 2026, [https://arxiv.org/html/2605.20173v1](https://arxiv.org/html/2605.20173v1)  
> 3. Compiled AI: Deterministic Code Generation for LLM-Based Workflow Automation - arXiv, accessed on June 2, 2026, [https://arxiv.org/html/2604.05150v1](https://arxiv.org/html/2604.05150v1)  
> 4. 𝖲𝖺𝗀𝖺𝖫𝖫𝖬: Context Management, Validation, and Transaction Guarantees for Multi-Agent LLM Planning - arXiv, accessed on June 2, 2026, [https://arxiv.org/html/2503.11951v2](https://arxiv.org/html/2503.11951v2)  
> 5. \[Papierüberprüfung\] Robust Agent Compensation (RAC): Teaching AI Agents to Compensate - Moonlight, accessed on June 2, 2026, [https://www.themoonlight.io/de/review/robust-agent-compensation-rac-teaching-ai-agents-to-compensate](https://www.themoonlight.io/de/review/robust-agent-compensation-rac-teaching-ai-agents-to-compensate)  
> 6. Robust Agent Compensation (RAC): Teaching AI Agents to Compensate - arXiv, accessed on June 2, 2026, [https://arxiv.org/html/2605.03409v1](https://arxiv.org/html/2605.03409v1)  
> 7. \[2605.20173\] A Methodology for Selecting and Composing Runtime Architecture Patterns for Production LLM Agents - arXiv, accessed on June 2, 2026, [https://arxiv.org/abs/2605.20173](https://arxiv.org/abs/2605.20173)  
> 8. Robust Agent Compensation (RAC): Teaching AI Agents to Compensate - arXiv, accessed on June 2, 2026, [https://arxiv.org/pdf/2605.03409](https://arxiv.org/pdf/2605.03409)  
> 9. Deterministic Legal Agents: A Canonical Primitive API for Auditable Reasoning over Temporal Knowledge Graphs - arXiv, accessed on June 2, 2026, [https://arxiv.org/html/2510.06002v3](https://arxiv.org/html/2510.06002v3)  
> 10. Architecting LLM Agents: The SDB Primitive | StartupHub.ai, accessed on June 2, 2026, [https://www.startuphub.ai/ai-news/ai-research/2026/architecting-llm-agents-the-sdb-primitive](https://www.startuphub.ai/ai-news/ai-research/2026/architecting-llm-agents-the-sdb-primitive)  
> 11. Defense Against Indirect Prompt Injection via Tool Result Parsing - arXiv, accessed on June 2, 2026, [https://arxiv.org/html/2601.04795v1](https://arxiv.org/html/2601.04795v1)  
> 12. Indirect Prompt Injections: Are Firewalls All You Need, or Stronger Benchmarks? - arXiv, accessed on June 2, 2026, [https://arxiv.org/pdf/2510.05244](https://arxiv.org/pdf/2510.05244)  
> 13. Indirect Prompt Injection: How It Works & Why It's Dangerous - Witness AI, accessed on June 2, 2026, [https://witness.ai/blog/indirect-prompt-injection/](https://witness.ai/blog/indirect-prompt-injection/)  
> 14. What is Indirect Prompt Injection? Risks & Prevention - SentinelOne, accessed on June 2, 2026, [https://www.sentinelone.com/cybersecurity-101/cybersecurity/indirect-prompt-injection-attacks/](https://www.sentinelone.com/cybersecurity-101/cybersecurity/indirect-prompt-injection-attacks/)  
> 15. Defense against Prompt Injection Attacks via Mixture of Encodings - ACL Anthology, accessed on June 2, 2026, [https://aclanthology.org/2025.naacl-short.21/](https://aclanthology.org/2025.naacl-short.21/)  
> 16. Indirect Prompt Injections: Are Firewalls All You Need, or Stronger Benchmarks? - arXiv, accessed on June 2, 2026, [https://arxiv.org/html/2510.05244v1](https://arxiv.org/html/2510.05244v1)  
> 17. Indirect Prompt Injections: Are Firewalls All You Need, or Stronger Benchmarks? - OpenReview, accessed on June 2, 2026, [https://openreview.net/pdf?id=aSUHAayPml](https://openreview.net/pdf?id=aSUHAayPml)  
> 18. Robust Agent Compensation (RAC): Teaching AI Agents to Compensate - arXiv, accessed on June 2, 2026, [https://arxiv.org/html/2605.03409v2](https://arxiv.org/html/2605.03409v2)  
> 19. ATLAS-RTC: Closing the Loop on LLM Agent Output with Token-Level Runtime Control, accessed on June 2, 2026, [https://arxiv.org/html/2603.27905v1](https://arxiv.org/html/2603.27905v1)  
> 20. ATLAS-RTC: Closing the Loop on LLM Agent Output with Token-Level Runtime Control, accessed on June 2, 2026, [https://www.researchgate.net/publication/403307248\_ATLAS-RTC\_Closing\_the\_Loop\_on\_LLM\_Agent\_Output\_with\_Token-Level\_Runtime\_Control](https://www.researchgate.net/publication/403307248_ATLAS-RTC_Closing_the_Loop_on_LLM_Agent_Output_with_Token-Level_Runtime_Control)  
> 21. \[2603.27905\] ATLAS-RTC: Closing the Loop on LLM Agent Output with Token-Level Runtime Control - arXiv, accessed on June 2, 2026, [https://arxiv.org/abs/2603.27905](https://arxiv.org/abs/2603.27905)  
> 22. Adapting the Interface, Not the Model: Runtime Harness Adaptation for Deterministic LLM Agents - arXiv, accessed on June 2, 2026, [https://arxiv.org/html/2605.22166v1](https://arxiv.org/html/2605.22166v1)  
> 23. Tianshi-Xu/Life-Harness - GitHub, accessed on June 2, 2026, [https://github.com/Tianshi-Xu/Life-Harness](https://github.com/Tianshi-Xu/Life-Harness)  
> 24. Adapting the Interface, Not the Model: Runtime Harness Adaptation for Deterministic LLM Agents - ResearchGate, accessed on June 2, 2026, [https://www.researchgate.net/publication/405132031\_Adapting\_the\_Interface\_Not\_the\_Model\_Runtime\_Harness\_Adaptation\_for\_Deterministic\_LLM\_Agents](https://www.researchgate.net/publication/405132031_Adapting_the_Interface_Not_the_Model_Runtime_Harness_Adaptation_for_Deterministic_LLM_Agents)  
> 25. ClawTrace: Cost-Aware Tracing for LLM Agent Skill Distillation - arXiv, accessed on June 2, 2026, [https://arxiv.org/html/2604.23853v1](https://arxiv.org/html/2604.23853v1)  
> 26. ClawTrace: Cost-Aware Tracing for LLM Agent Skill Distillation - OpenReview, accessed on June 2, 2026, [https://openreview.net/pdf/ff59b352640c77bf7bcac60375cbe33874503f82.pdf](https://openreview.net/pdf/ff59b352640c77bf7bcac60375cbe33874503f82.pdf)  
> 27. ClawTrace: Cost-Aware Tracing for LLM Agent Skill Distillation - arXiv, accessed on June 2, 2026, [https://arxiv.org/html/2604.23853v2](https://arxiv.org/html/2604.23853v2)  
> 28. Trace2Skill: Distill Trajectory-Local Lessons into Transferable Agent Skills - arXiv, accessed on June 2, 2026, [https://arxiv.org/html/2603.25158v1](https://arxiv.org/html/2603.25158v1)  
> 29. XGrammar-2: Fast and Customizable Structured Generation for Tool Calling and Agents, accessed on June 2, 2026, [https://blog.mlc.ai/2026/05/04/xgrammar-2-fast-customizable-structured-generation](https://blog.mlc.ai/2026/05/04/xgrammar-2-fast-customizable-structured-generation)  
> 30. Grammar-Constrained Generation: The Output Reliability Technique Most Teams Skip, accessed on June 2, 2026, [https://tianpan.co/blog/2026-04-16-grammar-constrained-generation-output-reliability](https://tianpan.co/blog/2026-04-16-grammar-constrained-generation-output-reliability)  
> 31. When Correct Isn't Usable: Improving Structured Output Reliability in Small Language Models - arXiv, accessed on June 2, 2026, [https://arxiv.org/html/2605.02363v1](https://arxiv.org/html/2605.02363v1)  
> 32. XGrammar 2: High-Performance Grammar Systems - Emergent Mind, accessed on June 2, 2026, [https://www.emergentmind.com/topics/xgrammar-2](https://www.emergentmind.com/topics/xgrammar-2)  
> 33. vLLM vs SGLang 2026: H100 Benchmarks Inside | TECHSY, accessed on June 2, 2026, [https://techsy.io/en/blog/vllm-vs-sglang](https://techsy.io/en/blog/vllm-vs-sglang)  
> 34. Generating Structured Outputs from Language Models: Benchmark and Studies - arXiv, accessed on June 2, 2026, [https://arxiv.org/html/2501.10868v1](https://arxiv.org/html/2501.10868v1)  
> 35. Proposal: Agent Compensation Middleware for Multi-Step Rollback for workflows · Issue \#37245 - GitHub, accessed on June 2, 2026, [https://github.com/langchain-ai/langchain/issues/37245](https://github.com/langchain-ai/langchain/issues/37245)  
> 36. SagaLLM: Context Management, Validation, and Transaction Guarantees for Multi-Agent LLM Planning - VLDB Endowment, accessed on June 2, 2026, [https://www.vldb.org/pvldb/vol18/p4874-chang.pdf](https://www.vldb.org/pvldb/vol18/p4874-chang.pdf)  
> 37. (PDF) SagaLLM: Context Management, Validation, and Transaction Guarantees for Multi-Agent LLM Planning - ResearchGate, accessed on June 2, 2026, [https://www.researchgate.net/publication/389917083\_SagaLLM\_Context\_Management\_Validation\_and\_Transaction\_Guarantees\_for\_Multi-Agent\_LLM\_Planning](https://www.researchgate.net/publication/389917083_SagaLLM_Context_Management_Validation_and_Transaction_Guarantees_for_Multi-Agent_LLM_Planning)  
> 38. What Is a Prompt Injection Attack? Explained - Commvault, accessed on June 2, 2026, [https://www.commvault.com/explore/prompt-injection-attacks](https://www.commvault.com/explore/prompt-injection-attacks)  
> 39. Fooling AI Agents: Web-Based Indirect Prompt Injection Observed in the Wild, accessed on June 2, 2026, [https://unit42.paloaltonetworks.com/ai-agent-prompt-injection/](https://unit42.paloaltonetworks.com/ai-agent-prompt-injection/)  
> 40. Token Cost Optimization in an LLM Agent for JSON-Based System Modeling from Dialogue History - ResearchGate, accessed on June 2, 2026, [https://www.researchgate.net/publication/404252744\_Token\_Cost\_Optimization\_in\_an\_LLM\_Agent\_for\_JSON-Based\_System\_Modeling\_from\_Dialogue\_History](https://www.researchgate.net/publication/404252744_Token_Cost_Optimization_in_an_LLM_Agent_for_JSON-Based_System_Modeling_from_Dialogue_History)
