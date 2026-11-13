---
title: "AROS: Autonomous Recursive Operations System"
kind: docs
status: stable
summary: Architectural blueprint for a self-hosted autonomous recursive operations system (AROS).
tags: [agents, self-hosting, architecture]
created: 2026-03-21
updated: 2026-03-21
origin: "https://docs.google.com/document/d/1NAzdk8ddmEUTYd_s4A1qdWs_37VW7sWT3WguoGJ8U_c"
---

# **Architectural Blueprint: Autonomous Recursive Operations System (AROS)**

## **1. MACRO DECONSTRUCTION (The "What" & "Why")**

The core essence of the target architecture is a locally hosted, entirely uncensored, closed-loop artificial intelligence ecosystem capable of autonomous code execution, dynamic tool generation, and recursive self-improvement without external API dependencies.

Your initial premise of "playing with self-hosted LLMs" to build an automated system relies on a fundamentally flawed paradigm if you view the large language model (LLM) as the entire system. An LLM is merely a static, non-deterministic next-token predictor; it cannot act, evolve, or execute. The logically sound alternative is to treat the LLM strictly as the "cognitive CPU" within a broader, deterministic software architecture. This architecture must encompass memory retrieval systems, isolated code-execution sandboxes, and programmatic evaluation loops.

The ultimate desired state is a sovereign "meta-agent" running continuously on your AMD Radeon AI PRO R9700 compute node. This agent must be capable of receiving a high-level macro-objective, autonomously decomposing it into sub-tasks, writing the necessary Python tools to execute those tasks, testing the tools within a secure microVM, analyzing terminal errors to self-correct its logic, and permanently registering successful tools into a vector database for future use, thereby achieving true, self-directed operational evolution.

## **2. RECURSIVE FRAGMENTATION (The "Atoms")**

To construct an autonomous, self-evolving system bound by the physical constraints of an AMD Radeon AI PRO R9700 (32GB VRAM), 64GB of system RAM, and 8TB of storage, the architecture must be ruthlessly fragmented into four structural pillars. Each pillar addresses a distinct layer of the compute, cognitive, and execution stack.

## **Level 1: The Pillars**

The infrastructure relies on four primary components. The first is Hardware and Silicon Orchestration, which dictates how the Linux kernel interfaces with the RDNA 4 architecture to maximize memory bandwidth and compute utilization. The second is Cognitive Engine Selection, defining the exact deployment of uncensored, abliterated models configured to fit within exact VRAM boundaries while retaining state-of-the-art coding capabilities. The third is the Sandboxed Execution Matrix, the critical security layer that allows an unrestricted intelligence to execute arbitrary code without compromising the host machine. The final pillar is the Recursive Evolution Loop, the programmatic Python framework that bridges the cognitive engine with the execution matrix to enable autonomous learning and tool retention.

## **Level 2 & 3: The Gears and Atoms**

The following subsections break down each pillar into actionable sub-problems (Gears) and discrete structural elements (Atoms).

#### **Pillar 1: Hardware and Silicon Orchestration**

The physical compute layer must be optimized to squeeze every available teraflop out of the R9700 while meticulously managing the 32GB VRAM ceiling.

**Gear 1.1: Core Driver and Kernel Optimization** The interface between the operating system and the GPU dictates baseline performance. Windows subsystem routing introduces unacceptable latency and memory overhead for multi-agent workflows, making bare-metal Linux mandatory.1

* **Atom 1.1.1: Ubuntu 24.04 LTS Base OS**  
  * **What is it?** The foundational Linux operating system kernel required for native AMD compute deployment.3  
  * **What is its purpose (Why)?** Advanced Micro Devices (AMD) develops ROCm drivers and PyTorch binaries primarily for Ubuntu LTS releases.5 The host must avoid full disk encryption, as it introduces I/O latency that severely bottlenecks data retrieval when loading massive tensor files from the 8TB NVMe array into VRAM.7  
  * **What does it lead to (Outcome)?** Bare-metal access to the PCIe 5.0 x16 bus, unfragmented memory allocation, and maximum data transfer rates between the 64GB system RAM and the GPU.  
* **Atom 1.1.2: ROCm 7.1.1 Ecosystem**  
  * **What is it?** AMD's open-source software platform for GPU compute, specifically optimized for the RDNA 4 architecture of the R9700.1  
  * **What is its purpose (Why)?** ROCm 7.1.1 translates high-level PyTorch tensor operations into low-level hardware instructions. It provides critical support for FP8 precision and multi-head attention optimizations required to run modern models efficiently.5  
  * **What does it lead to (Outcome)?** Full utilization of the R9700's 128 AI accelerators, yielding up to 95.7 TFLOPS of FP16 compute and unlocking the 300W TDP limit for sustained 24/7 inference workloads.8  
* **Atom 1.1.3: Environmental Tensor Overrides**  
  * **What is it?** Specific system-level environment variables injected into the Linux .bashrc profile, such as PYTORCH\_TUNABLEOP\_ENABLED=1 and HSA\_OVERRIDE\_GFX\_VERSION=11.0.0.4  
  * **What is its purpose (Why)?** These variables force the PyTorch ROCm backend to empirically test and select the fastest Matrix Multiply (GEMM) algorithms for the specific local hardware layout.10 Furthermore, variables like GPU\_SINGLE\_ALLOC\_PERCENT=100 prevent the driver from artificially limiting memory allocations during massive context spikes.11  
  * **What does it lead to (Outcome)?** Maximum token-per-second generation throughput and the elimination of Out-Of-Memory (OOM) fragmentation crashes.

**Gear 1.2: VRAM Management and Inference Backends** Running a complex model requires fitting both the model weights and the context window (Key-Value cache) into the R9700's 32GB of GDDR6 memory.5

* **Atom 1.2.1: GGUF Quantization Engine (Ollama)**  
  * **What is it?** A localized inference server built on llama.cpp that executes highly compressed model weights.12  
  * **What is its purpose (Why)?** A native 32-billion parameter model in FP16 precision requires over 64GB of VRAM, which exceeds your hardware limits. By utilizing Q4\_K\_M (4-bit) quantization via the GGUF format, the model size is reduced to approximately 20.5GB.14 Ollama is selected over vLLM for single-GPU deployments because it seamlessly manages dynamic memory offloading; if the context window overflows the remaining 11.5GB of VRAM, Ollama gracefully offloads the excess to your 64GB of system RAM rather than crashing.12  
  * **What does it lead to (Outcome)?** The ability to run intelligence engines that mathematically shouldn't fit on your hardware, maintaining high speed while preserving the remaining VRAM for agentic scratchpads.

#### **Pillar 2: Cognitive Engine Selection**

Your request specifically demanded an analysis of the top uncensored models suited for a 32GB VRAM setup capable of autonomous tool creation.16 For an agentic framework to succeed, it requires models that excel at structured output, programming logic, and function calling.18 The hardware strictly dictates that the primary reasoning engine must reside in the 32-billion parameter class to fit within the memory envelope while leaving room for extensive context.20

| Model Identifier | Architecture | Parameters | VRAM Footprint (Q4\_K\_M) | Optimal Use Case |
| :---- | :---- | :---- | :---- | :---- |
| **Qwen2.5-Coder-32B-Instruct (Abliterated)** | Dense | 32.5B | ~20.5 GB | Primary autonomous architect; advanced code generation and debugging. |
| **DeepSeek-R1-Distill-Qwen-32B (Ablated)** | Dense / CoT | 32.5B | ~20.5 GB | Deep logical reasoning; decomposing undefined macro-problems. |
| **Dolphin 3.0 Llama 3.1 8B** | Dense | 8B | ~5.0 GB | High-speed semantic routing; basic function calling and system administration. |
| **Mistral Small 3.1 24B Instruct** | Dense | 24B | ~14.5 GB | Balanced instruction following; fast iteration and rapid response. |
| **LLaMA-3.2 Dark Champion Abliterated** | Mixture of Experts (MoE) | 8x3B (24B active) | ~15.0 GB | Massive 128k context analysis; document retrieval and unaligned storytelling. |

**Gear 2.1: The Primary Execution Engines**

These models serve as the system's prefrontal cortex, responsible for writing the Python tools and executing complex logic.

* **Atom 2.1.1: Qwen2.5-Coder-32B-Instruct (Uncensored)**  
  * **What is it?** A massive 32.5-billion parameter foundational model trained on over 5.5 trillion tokens of code and technical text.22 The community-abliterated version strips away safety alignments, ensuring zero refusal rates.20  
  * **What is its purpose (Why)?** Qwen2.5-Coder matches proprietary models like GPT-4o in pure programming benchmarks.22 In empirical tests, Qwen dominates local models in function calling accuracy, making it the most reliable engine for deciding when and how to execute tools.18 At a Q4\_K\_M quantization, it consumes 20.5GB of VRAM, leaving 11.5GB of your 32GB R9700 strictly for its massive 128,000-token context window.14  
  * **What does it lead to (Outcome)?** The flawless generation of complex Python scripts, API integration logic, and system-level commands required for the agent to manipulate its environment.  
* **Atom 2.1.2: DeepSeek-R1-Distill-Qwen-32B (Ablated)**  
  * **What is it?** A reasoning-focused model that inherits the Chain-of-Thought (CoT) reinforcement learning patterns of the massive DeepSeek-R1, distilled into the highly efficient Qwen 32B architecture.24  
  * **What is its purpose (Why)?** While Qwen2.5-Coder is superior at immediate syntax generation, DeepSeek-R1 excels at prolonged mathematical deduction and strategic planning.26 When the system encounters a novel problem it has never seen before, DeepSeek-R1 is invoked to "think" in an internal scratchpad before outputting an action plan.25  
  * **What does it lead to (Outcome)?** High-level strategic problem solving and the ability to autonomously navigate dead-ends during execution.

**Gear 2.2: The Secondary Utility Engines**

Relying entirely on a 32B model for trivial tasks wastes computational power and time. The system utilizes smaller, highly specialized models for rapid operations.

* **Atom 2.2.1: Dolphin 3.0 (Llama 3.1 8B)**  
  * **What is it?** An 8-billion parameter model developed by Cognitive Computations, explicitly designed to be completely uncensored and highly adept at function calling.20  
  * **What is its purpose (Why)?** With a VRAM footprint of only 5GB, this model generates text at blistering speeds on the R9700.21 It is utilized by the orchestration loop for low-level tasks such as reading files, categorizing terminal errors, and routing user queries to the appropriate tools.20  
  * **What does it lead to (Outcome)?** A massively reduced latency overhead for routine system maintenance, acting as the nervous system connecting the user to the heavier 32B brain.

#### **Pillar 3: The Sandboxed Execution Matrix**

To grant an uncensored AI the ability to write and execute code is to invite catastrophic system failure. If the agent hallucinates a destructive bash command or writes a recursive loop that consumes all system resources, it will destroy the host operating system. The execution matrix provides a mathematically proven containment field.30

**Gear 3.1: Kernel-Level Isolation**

The execution environment must mirror the host to be useful but remain cryptographically isolated to be safe.

* **Atom 3.1.1: Docker MicroVM Sandboxes**  
  * **What is it?** A lightweight, ephemeral virtualization layer leveraging Docker-in-Docker technology, providing the agent with a dedicated, disposable kernel.31  
  * **What is its purpose (Why)?** Traditional OS-level protections are insufficient for an agent that dynamically requires new system packages.33 The microVM allows the agent to execute in "YOLO-mode"—running commands with root privileges *inside* the sandbox without any capability to escalate those privileges to your host Ubuntu OS.31 If the agent destroys the environment, the Python orchestration loop simply purges the container and spins up a pristine clone in milliseconds.32  
  * **What does it lead to (Outcome)?** Absolute confidence in granting the AI total autonomy to test, break, and iterate its own code.  
* **Atom 3.1.2: Strict Volume Bind Mounts**  
  * **What is it?** A specific directory mapping (e.g., /home/skynet/tools on the host mapped to /app/tools in the container) that bridges the isolation gap.32  
  * **What is its purpose (Why)?** The LLM runs on the host to access the GPU, but the code executes in the sandbox. The bind mount is the only physical location where the two environments touch. The agent writes a Python script to this specific folder, the sandbox reads and executes it, and the terminal output is piped back to the host.31  
  * **What does it lead to (Outcome)?** Secure, asynchronous communication between the intelligence engine and its hands.

#### **Pillar 4: The Recursive Evolution Loop**

For an AI to mimic a "Skynet" infrastructure, it cannot remain static. It must possess a mechanism to recognize failure, patch its own logic, and permanently memorize successful operations.35 This requires a dedicated Python orchestration layer.

**Gear 4.1: The Self-Healing Error Protocol**

Code generated by LLMs fails frequently. The system must eliminate the human debugger.

* **Atom 4.1.1: The Execution-Evaluation Circuit**  
  * **What is it?** A programmatic loop based on the ReAct (Reason + Act) methodology that captures the stderr output of any failed command run in the sandbox.37  
  * **What is its purpose (Why)?** When a dynamically generated tool crashes, the loop intercepts the stack trace. It wraps this trace in an automated prompt (e.g., *"Execution failed at line 42 with KeyError. Analyze and rewrite."*) and feeds it back to the Qwen 32B model.38  
  * **What does it lead to (Outcome)?** Autonomous debugging. The system loops recursively, continuously editing its own code until it receives an exit code of 0 from the sandbox.38

**Gear 4.2: Permanent Memory and Tool Registration**

Once a tool works, the system must retain it forever, eliminating the need to rewrite the same logic in the future.

* **Atom 4.2.1: Abstract Syntax Tree (AST) Parsing**  
  * **What is it?** A built-in Python library utilized by the orchestration script to read newly generated .py files.41  
  * **What is its purpose (Why)?** For an LLM to use a tool via function calling, it requires a strict JSON Schema detailing the function's name, arguments, and purpose.37 The AST parser programmatically extracts this metadata from the successful code and automatically registers it into the agent's known capabilities without requiring a system restart.41  
  * **What does it lead to (Outcome)?** An ever-expanding, auto-updating library of agent capabilities.  
* **Atom 4.2.2: Semantic Vector Database Retrieval (ChromaDB)**  
  * **What is it?** A local, open-source database designed to store mathematical representations (embeddings) of text.29  
  * **What is its purpose (Why)?** As the agent writes hundreds of specialized tools over time, passing all 500 JSON schemas into the context window will exhaust the VRAM and confuse the LLM.42 Instead, when the agent receives a task, it queries ChromaDB. The database performs a semantic search (e.g., matching "download video" to yt\_dlp\_wrapper.py) and dynamically injects only the top three most relevant tool schemas into the current context.42  
  * **What does it lead to (Outcome)?** Infinite scalability of the agent's skillset without degrading cognitive reasoning performance.

## ---

**3. THE MULTIVERSE ANALYSIS (Options & Trade-offs)**

To deploy this local intelligence ecosystem, three distinct architectural paradigms present themselves. Each path represents a different balance of user involvement, system complexity, and true autonomy.

| Architecture Path | Core Methodology | Primary Advantages | Critical Disadvantages | Unique Capabilities Unlocked |
| :---- | :---- | :---- | :---- | :---- |
| **Path A: The Swarm** | CrewAI / AutoGen utilizing multiple specialized 8B models working in parallel. | Fast execution; enforces distinct roles (Manager, Coder, Reviewer).45 | High VRAM swapping overhead. Smaller models hallucinate frequently. No native secure code execution environments.45 | Parallel task delegation and conversational brainstorming among agents. |
| **Path B: The Terminal Assistant** | Aider / OpenHands CLI orchestrating a single 32B model connected to git workflows.47 | Dead-simple setup. Seamless integration with existing software projects. Strong diff application.49 | Primarily reactive. Requires human initiation. Fails to autonomously generate and persist generic non-coding tools over time.49 | Rapid pair programming and immediate codebase refactoring. |
| **Path C: The Sovereign Meta-Agent** | Custom Python Orchestrator (LMAgent/LangGraph) + Qwen 32B + Docker MicroVMs + ChromaDB.43 | **Total autonomy.** Can write code, test it securely, debug itself, and permanently expand its own capabilities.35 | Immense initial configuration complexity. High risk of infinite execution loops if exit conditions are poorly defined. | The requested "Skynet" dynamic. True self-evolution, perpetual operation, and unbounded tool creation. |

## **The Optimal Selection: Path C (The Sovereign Meta-Agent)**

Path A is fundamentally constrained by your hardware; running three distinct 8B models simultaneously to simulate a team will shatter your 32GB VRAM limit, causing the system to offload to the 64GB system RAM, destroying execution speed. Path B is exceptional for human-in-the-loop software development but fails to meet your requirement for an autonomous system that "writes its own tools and figures out problems" entirely on its own.

**Path C** is the absolute, logically sound approach. It centralizes all computational resources into a single, massive reasoning engine (Qwen2.5-Coder-32B or DeepSeek-R1-Distill-32B). It leverages a lightweight Python script to act as the agent's "consciousness," constantly looping between the LLM, the secure Docker execution sandbox, and the persistent vector memory bank. This architecture provides the exact localized, uncensored, self-improving automation engine you requested, mapping perfectly to the capabilities of the R9700.

## ---

**4. THE MASTER PLAN (Execution)**

The following is the chronological, zero-fluff execution protocol required to build the Autonomous Recursive Operations System on your hardware. Deviations from this sequence will result in dependency conflicts or catastrophic VRAM overallocation.

## **Phase 1: Hardware Optimization and Baseline Ecosystem**

You must strip away unnecessary computational overhead and establish a direct conduit to the RDNA 4 silicon.

* **Action:** Provision Ubuntu 24.04 LTS natively and format the 8TB NVMe array using the ext4 or ZFS filesystem without full disk encryption.  
  * **Methodology (How):** Execute a standard minimal installation of Ubuntu Server or Desktop.3  
  * **Reasoning (Why):** AMD ROCm drivers are explicitly validated against Ubuntu LTS releases.4 The omission of full disk encryption prevents I/O bottlenecking; retrieving a 20GB quantized model file from the disk into VRAM must occur instantaneously to maintain low latency during agent cold starts.7  
* **Action:** Install the AMD ROCm 7.1.1 framework and PyTorch bindings.  
  * **Methodology (How):** Download the official amdgpu-install utility. Execute sudo amdgpu-install --usecase=rocm,hip,mivisionx. Add your user to the render and video groups to ensure hardware access without root.5  
  * **Reasoning (Why):** ROCm is the proprietary software stack that allows AI frameworks to utilize the Radeon compute units.52 Version 7.1.1 contains specific architectural patches for RDNA 4 and FP8 precision, maximizing the TFLOPS output of the R9700.5  
* **Action:** Inject aggressive environment variables into the system profile.  
  * **Methodology (How):** Append the following directives to your ~/.bashrc file:  
    export HSA\_OVERRIDE\_GFX\_VERSION=11.0.0  
    export PYTORCH\_TUNABLEOP\_ENABLED=1  
    export HIP\_VISIBLE\_DEVICES=0  
    export GPU\_SINGLE\_ALLOC\_PERCENT=100  
  * **Reasoning (Why):** PYTORCH\_TUNABLEOP\_ENABLED forces the ROCm backend to empirically profile and select the most highly optimized matrix multiplication algorithms for your specific silicon, bypassing generic defaults.10 GPU\_SINGLE\_ALLOC\_PERCENT prevents the driver from arbitrarily limiting memory chunks, ensuring the LLM can claim the entire 32GB VRAM space if required by a massive context window.11

## **Phase 2: Inference Engine and Model Deployment**

The system requires an inference backend capable of managing quantization and dynamic VRAM offloading perfectly.

* **Action:** Deploy the Ollama inference server.  
  * **Methodology (How):** Execute curl -fsSL https://ollama.com/install.sh | sh. Verify the daemon is running and recognizes the AMD GPU via ollama ps.  
  * **Reasoning (Why):** While vLLM is superior for multi-user enterprise servers, Ollama utilizes llama.cpp as its core, which provides unparalleled support for GGUF quantization formats.12 More importantly, if your context window causes the VRAM to spike past 32GB, Ollama will dynamically offload the excess layers to your 64GB of system RAM, preventing a hard crash.13  
* **Action:** Pull and configure the primary cognitive engines.  
  * **Methodology (How):** Execute the following commands:  
    ollama pull hf.co/bartowski/Qwen2.5-Coder-32B-Instruct-GGUF:Q4\_K\_M  
    ollama pull dolphin-llama3  
  * **Reasoning (Why):** You are provisioning the optimal brains for the system. Qwen2.5-Coder-32B at Q4\_K\_M compression consumes ~20.5GB of VRAM.14 This guarantees state-of-the-art coding logic while reserving exactly 11.5GB of VRAM to maintain a massive context history of the agent's actions. The Dolphin model serves as an ultra-fast, 5GB utility router for secondary tasks.20 Both models are fundamentally uncensored, adhering to your request for unrestricted capabilities.16

## **Phase 3: Constructing the Execution Matrix**

The system must be granted a secure playground to execute the tools it invents.

* **Action:** Instantiate a Docker MicroVM sandbox.  
  * **Methodology (How):** Install Docker Engine. Create an isolated Docker network using docker network create --internal sandbox\_net. Build a generic execution container using python:3.11-slim as the base image.53  
  * **Reasoning (Why):** Unrestricted code execution is highly dangerous. The Docker sandbox provides a disposable, isolated kernel. The agent operates in "YOLO mode" here; it can execute destructive commands, install random pip packages, and manipulate files without any risk of affecting the underlying Ubuntu 24.04 host.31  
* **Action:** Map the cryptographic communication bridge via bind mounts.  
  * **Methodology (How):** Map a dedicated host directory to the sandbox upon launch: docker run -v /opt/aros/tools:/workspace/tools -v /opt/aros/workspace:/workspace/data...  
  * **Reasoning (Why):** The Python orchestration script running on the host needs a mechanism to pass the generated code to the isolated sandbox. The bind mount serves as the physical interface; the LLM writes a file to the host directory, the sandbox detects it, executes it, and writes the stdout/stderr logs back to the directory for the LLM to read.31

## **Phase 4: Implementing the Recursive Orchestration Loop**

The intelligence is made active through a continuous Python script that acts as the agent's consciousness.

* **Action:** Write the core ReAct (Reason and Act) loop using a localized framework.  
  * **Methodology (How):** Utilize Python with pydantic-ai or deploy the lightweight LMAgent framework.41 Program the loop to function sequentially:  
    1. Parse the user objective.  
    2. Query ChromaDB for relevant existing tools.  
    3. Pass the objective and tools to Qwen2.5-Coder-32B.  
    4. If a tool is missing, instruct Qwen to write the Python code for it.  
    5. Push the code to the Docker sandbox and execute.  
  * **Reasoning (Why):** LLMs cannot inherently "do" anything. The Python loop provides the necessary agency, converting static text generation into a dynamic observation-and-action cycle.37  
* **Action:** Engineer the "LLM-as-a-Judge" self-healing subroutine.  
  * **Methodology (How):** Modify the execution script to catch any non-zero exit codes (errors) from the Docker sandbox. When an error is caught, automatically format the stack trace into a strict prompt: *"Execution of failed with. Analyze the logical failure and output the corrected Python code."* Route this back to the primary LLM.38  
  * **Reasoning (Why):** This is the foundation of self-improvement. It eliminates the need for you to debug the AI's code. The system will autonomously loop, patching its own logic until the script executes successfully.38

## **Phase 5: The Permanent Evolution Protocol**

The final step ensures the system permanently learns from its actions, mimicking the requested "Skynet" trajectory.

* **Action:** Deploy the Semantic Vector Memory Bank.  
  * **Methodology (How):** Initialize a localized ChromaDB instance. Pull the nomic-embed-text model via Ollama to generate embeddings.29  
  * **Reasoning (Why):** As the agent successfully writes and debugs hundreds of tools, it cannot keep them all in its active memory (the context window). The vector database stores the tools conceptually. When faced with a new problem, the agent performs a semantic search to retrieve only the exact tools necessary for the current operation, ensuring infinite scalability.42  
* **Action:** Automate Abstract Syntax Tree (AST) Tool Registration.  
  * **Methodology (How):** Implement a Python watchdog within your orchestration script. When the sandbox reports a successful code execution for a newly generated tool, use the ast library to parse the raw Python file. Extract the function name, arguments, and docstrings, and format them into a strict JSON Schema.41  
  * **Reasoning (Why):** The LLM must explicitly understand how to use the tool it just invented in future iterations.37 The AST parser programmatically converts raw code into the standardized schema required for function calling, appending it to the ChromaDB database.41 This completes the closed loop: the agent encounters a barrier, writes a tool to overcome it, validates the tool, and permanently registers it into its own cognitive architecture without human intervention.

#### **Works cited**

> 1. How to setup ComfyUI AI Image Generation on your AMD RDNA 4 GPU using ROCm 7.1.1 on Windows - Wccftech, accessed on March 21, 2026, [https://wccftech.com/how-to-setup-comfyui-ai-image-generation-on-amd-rdna-4-gpu-rocm-7-1-1-windows/](https://wccftech.com/how-to-setup-comfyui-ai-image-generation-on-amd-rdna-4-gpu-rocm-7-1-1-windows/)  
> 2. \[Setup discussion\] AMD RX 7900 XTX workstation for local LLMs — Linux or Windows as host OS? : r/LocalLLaMA - Reddit, accessed on March 21, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1lfhdnb/setup\_discussion\_amd\_rx\_7900\_xtx\_workstation\_for/](https://www.reddit.com/r/LocalLLaMA/comments/1lfhdnb/setup_discussion_amd_rx_7900_xtx_workstation_for/)  
> 3. ASUS Turbo Radeon™ AI Pro R9700, accessed on March 21, 2026, [https://www.asus.com/motherboards-components/graphics-cards/turbo/turbo-ai-pro-r9700-32g/](https://www.asus.com/motherboards-components/graphics-cards/turbo/turbo-ai-pro-r9700-32g/)  
> 4. guide to run 2x 7900 xtx on latest rocm 7.1.x : r/LocalLLaMA - Reddit, accessed on March 21, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1pa2th8/guide\_to\_run\_2x\_7900\_xtx\_on\_latest\_rocm\_71x/](https://www.reddit.com/r/LocalLLaMA/comments/1pa2th8/guide_to_run_2x_7900_xtx_on_latest_rocm_71x/)  
> 5. AMD Radeon AI PRO R9700 Getting Started, accessed on March 21, 2026, [https://www.amd.com/content/dam/amd/en/documents/products/graphics/workstation/radeon-ai-pro-rocm-pytorch-guide.pdf](https://www.amd.com/content/dam/amd/en/documents/products/graphics/workstation/radeon-ai-pro-rocm-pytorch-guide.pdf)  
> 6. Trillion-Parameter LLM on an AMD Ryzen™ AI Max+ Cluster, accessed on March 21, 2026, [https://www.amd.com/en/developer/resources/technical-articles/2026/how-to-run-a-one-trillion-parameter-llm-locally-an-amd.html](https://www.amd.com/en/developer/resources/technical-articles/2026/how-to-run-a-one-trillion-parameter-llm-locally-an-amd.html)  
> 7. Quickstart Guide: Ollama With GPU Support (No ROCM Needed) - Framework Community, accessed on March 21, 2026, [https://community.frame.work/t/quickstart-guide-ollama-with-gpu-support-no-rocm-needed/79186](https://community.frame.work/t/quickstart-guide-ollama-with-gpu-support-no-rocm-needed/79186)  
> 8. Benchmarking the Radeon AI Pro R9700: AMD's “Red Team” Buys into On-Device AI, accessed on March 21, 2026, [https://hostkey.com/blog/142-benchmarking-the-radeon-ai-pro-r9700-amds-red-team-buys-into-on-device-ai/](https://hostkey.com/blog/142-benchmarking-the-radeon-ai-pro-r9700-amds-red-team-buys-into-on-device-ai/)  
> 9. Hey all, I tested out the R9700 for mixed use gaming and local LLM and I think this GPU is a dark horse of this generation : r/radeon - Reddit, accessed on March 21, 2026, [https://www.reddit.com/r/radeon/comments/1qzxpxu/hey\_all\_i\_tested\_out\_the\_r9700\_for\_mixed\_use/](https://www.reddit.com/r/radeon/comments/1qzxpxu/hey_all_i_tested_out_the_r9700_for_mixed_use/)  
> 10. Guide for running AI on AMD RDNA 4 GPUs with ROCm 7.1 on Linux (Updated with Performance Tuning) - GitHub Gist, accessed on March 21, 2026, [https://gist.github.com/apollo-mg/ecba6a0c29323325a7ac3babf08e53be](https://gist.github.com/apollo-mg/ecba6a0c29323325a7ac3babf08e53be)  
> 11. ROCm environment variables, accessed on March 21, 2026, [https://rocm.docs.amd.com/en/latest/reference/env-variables.html](https://rocm.docs.amd.com/en/latest/reference/env-variables.html)  
> 12. Local LLM Hosting: Complete 2025 Guide — Ollama, vLLM, LocalAI, Jan, LM Studio & More, accessed on March 21, 2026, [https://medium.com/@rosgluk/local-llm-hosting-complete-2025-guide-ollama-vllm-localai-jan-lm-studio-more-f98136ce7e4a](https://medium.com/@rosgluk/local-llm-hosting-complete-2025-guide-ollama-vllm-localai-jan-lm-studio-more-f98136ce7e4a)  
> 13. Top 5 Local LLM Tools and Models in 2026 - DEV Community, accessed on March 21, 2026, [https://dev.to/lightningdev123/top-5-local-llm-tools-and-models-in-2026-1ch5](https://dev.to/lightningdev123/top-5-local-llm-tools-and-models-in-2026-1ch5)  
> 14. bartowski/Qwen2.5-Coder-32B-Instruct-GGUF - Hugging Face, accessed on March 21, 2026, [https://huggingface.co/bartowski/Qwen2.5-Coder-32B-Instruct-GGUF](https://huggingface.co/bartowski/Qwen2.5-Coder-32B-Instruct-GGUF)  
> 15. The Complete Developer's Guide to Running LLMs Locally: From Ollama to Production, accessed on March 21, 2026, [https://www.sitepoint.com/local-llms-complete-guide/](https://www.sitepoint.com/local-llms-complete-guide/)  
> 16. Looking for truly uncensored LLM models for local use : r/LocalLLM - Reddit, accessed on March 21, 2026, [https://www.reddit.com/r/LocalLLM/comments/1rodkyu/looking\_for\_truly\_uncensored\_llm\_models\_for\_local/](https://www.reddit.com/r/LocalLLM/comments/1rodkyu/looking_for_truly_uncensored_llm_models_for_local/)  
> 17. Best uncensored open-source models (2024–2025) for roleplay + image generation? : r/LocalLLM - Reddit, accessed on March 21, 2026, [https://www.reddit.com/r/LocalLLM/comments/1o7qxwx/best\_uncensored\_opensource\_models\_20242025\_for/](https://www.reddit.com/r/LocalLLM/comments/1o7qxwx/best_uncensored_opensource_models_20242025_for/)  
> 18. Tool Calling with Local LLMs: A Practical Evaluation - Docker, accessed on March 21, 2026, [https://www.docker.com/blog/local-llm-tool-calling-a-practical-evaluation/](https://www.docker.com/blog/local-llm-tool-calling-a-practical-evaluation/)  
> 19. Writing effective tools for AI agents—using AI agents - Anthropic, accessed on March 21, 2026, [https://www.anthropic.com/engineering/writing-tools-for-agents](https://www.anthropic.com/engineering/writing-tools-for-agents)  
> 20. Top 10 LLMs with No Restrictions in 2026 - Apidog, accessed on March 21, 2026, [https://apidog.com/blog/llms-no-restrictions/](https://apidog.com/blog/llms-no-restrictions/)  
> 21. Best Local LLM Models 2026 | Developer Comparison - SitePoint, accessed on March 21, 2026, [https://www.sitepoint.com/best-local-llm-models-2026/](https://www.sitepoint.com/best-local-llm-models-2026/)  
> 22. Qwen/Qwen2.5-Coder-32B-Instruct - Hugging Face, accessed on March 21, 2026, [https://huggingface.co/Qwen/Qwen2.5-Coder-32B-Instruct](https://huggingface.co/Qwen/Qwen2.5-Coder-32B-Instruct)  
> 23. Qwen2.5-32B-Instruct may be the best model for 3090s right now. : r/LocalLLaMA - Reddit, accessed on March 21, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1flfh0p/qwen2532binstruct\_may\_be\_the\_best\_model\_for\_3090s/](https://www.reddit.com/r/LocalLLaMA/comments/1flfh0p/qwen2532binstruct_may_be_the_best_model_for_3090s/)  
> 24. DeepSeek R1 Distill Qwen 2.5 32B ablated (uncensored) : r/LocalLLaMA - Reddit, accessed on March 21, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1i8b8mw/deepseek\_r1\_distill\_qwen\_25\_32b\_ablated\_uncensored/](https://www.reddit.com/r/LocalLLaMA/comments/1i8b8mw/deepseek_r1_distill_qwen_25_32b_ablated_uncensored/)  
> 25. deepseek-ai/DeepSeek-R1-Distill-Qwen-32B - Hugging Face, accessed on March 21, 2026, [https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Qwen-32B](https://huggingface.co/deepseek-ai/DeepSeek-R1-Distill-Qwen-32B)  
> 26. R1 Distill Qwen 32B vs Qwen2.5 Coder 32B Instruct (Comparative Analysis) - Galaxy.ai Blog, accessed on March 21, 2026, [https://blog.galaxy.ai/compare/deepseek-r1-distill-qwen-32b-vs-qwen-2-5-coder-32b-instruct](https://blog.galaxy.ai/compare/deepseek-r1-distill-qwen-32b-vs-qwen-2-5-coder-32b-instruct)  
> 27. Which do you think is better: Deepseek-R1-32B vs QWQ-32b-preview? - Reddit, accessed on March 21, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1j2wzky/which\_do\_you\_think\_is\_better\_deepseekr132b\_vs/](https://www.reddit.com/r/LocalLLaMA/comments/1j2wzky/which_do_you_think_is_better_deepseekr132b_vs/)  
> 28. How to Run DeepSeek R1 on Your Computer (100% FREE) - YouTube, accessed on March 21, 2026, [https://www.youtube.com/watch?v=rzMEieMXYFA](https://www.youtube.com/watch?v=rzMEieMXYFA)  
> 29. How to Build Your Own Local AI: Create Free RAG and AI Agents with Qwen 3 and Ollama, accessed on March 21, 2026, [https://www.freecodecamp.org/news/build-a-local-ai/](https://www.freecodecamp.org/news/build-a-local-ai/)  
> 30. Beyond AI Agent Tools with LLM Sandbox, accessed on March 21, 2026, [https://cobusgreyling.medium.com/beyond-ai-agent-tools-with-llm-sandbox-2bd9b4cf148a](https://cobusgreyling.medium.com/beyond-ai-agent-tools-with-llm-sandbox-2bd9b4cf148a)  
> 31. Docker Sandboxes, accessed on March 21, 2026, [https://docs.docker.com/ai/sandboxes/](https://docs.docker.com/ai/sandboxes/)  
> 32. Run Gemini CLI Autonomous Agent inside a Docker Sandbox: The Ultimate Guide to Secure Coding Agents, accessed on March 21, 2026, [https://ifeanyidiaye.medium.com/run-gemini-cli-autonomous-agent-inside-a-docker-sandbox-the-ultimate-guide-to-secure-coding-agents-c9b5b87fe97b](https://ifeanyidiaye.medium.com/run-gemini-cli-autonomous-agent-inside-a-docker-sandbox-the-ultimate-guide-to-secure-coding-agents-c9b5b87fe97b)  
> 33. Docker Sandboxes: Run Claude Code and Other Coding Agents Unsupervised (but Safely), accessed on March 21, 2026, [https://www.docker.com/blog/docker-sandboxes-run-claude-code-and-other-coding-agents-unsupervised-but-safely/](https://www.docker.com/blog/docker-sandboxes-run-claude-code-and-other-coding-agents-unsupervised-but-safely/)  
> 34. A New Approach for Coding Agent Safety - Docker, accessed on March 21, 2026, [https://www.docker.com/blog/docker-sandboxes-a-new-approach-for-coding-agent-safety/](https://www.docker.com/blog/docker-sandboxes-a-new-approach-for-coding-agent-safety/)  
> 35. Self-Evolving AI Agents: The Future of Intelligent Software Is Here, accessed on March 21, 2026, [https://medium.com/@thomas\_78526/self-evolving-ai-agents-the-future-of-intelligent-software-is-here-4e995ecd4bc7](https://medium.com/@thomas_78526/self-evolving-ai-agents-the-future-of-intelligent-software-is-here-4e995ecd4bc7)  
> 36. I Built a Self-Evolving AI Agent That Writes Its Own Tools - DEV Community, accessed on March 21, 2026, [https://dev.to/elophanto/i-built-a-self-evolving-ai-agent-that-writes-its-own-tools-11i](https://dev.to/elophanto/i-built-a-self-evolving-ai-agent-that-writes-its-own-tools-11i)  
> 37. GitHub - pguso/ai-agents-from-scratch: Demystify AI agents by building them yourself. Local LLMs, no black boxes, real understanding of function calling, memory, and ReAct patterns., accessed on March 21, 2026, [https://github.com/pguso/ai-agents-from-scratch](https://github.com/pguso/ai-agents-from-scratch)  
> 38. \[with Code\] Part 1 : How i built a self healing AI agent | by Rakesh Sheshadri - Medium, accessed on March 21, 2026, [https://medium.com/the-ai-intelligence-index/with-code-part-1-how-i-built-a-self-healing-ai-agent-977a90b18c82](https://medium.com/the-ai-intelligence-index/with-code-part-1-how-i-built-a-self-healing-ai-agent-977a90b18c82)  
> 39. From Prompt Engineering to Autonomous Optimization: Designing a Self-Improving LLM System | by BK Park | Feb, 2026 | Medium, accessed on March 21, 2026, [https://medium.com/@bkpark\_96196/from-prompt-engineering-to-autonomous-optimization-designing-a-self-improving-llm-system-e90028c4cb48](https://medium.com/@bkpark_96196/from-prompt-engineering-to-autonomous-optimization-designing-a-self-improving-llm-system-e90028c4cb48)  
> 40. Self-Improving Coding Agents - AddyOsmani.com, accessed on March 21, 2026, [https://addyosmani.com/blog/self-improving-agents/](https://addyosmani.com/blog/self-improving-agents/)  
> 41. Dynamically Generating Pydantic AI Agent Tools | by Mudassar Majeed | Medium, accessed on March 21, 2026, [https://medium.com/@mudassarm30/dynamically-generating-pydantic-ai-agent-tools-c2fd5b8722c2](https://medium.com/@mudassarm30/dynamically-generating-pydantic-ai-agent-tools-c2fd5b8722c2)  
> 42. madhurprash/meta-tools-and-agents: This repository contains patterns and implementations for you to simply build your own agents and have those agents build tools and agents for various use cases. - GitHub, accessed on March 21, 2026, [https://github.com/madhurprash/meta-tools-and-agents](https://github.com/madhurprash/meta-tools-and-agents)  
> 43. The Architecture Behind Self-Improving AI Agents (Full Implementation Guide) - Medium, accessed on March 21, 2026, [https://medium.com/@rselvaguru/the-architecture-behind-self-improving-ai-agents-full-implementation-guide-dfb0ce67c7c7](https://medium.com/@rselvaguru/the-architecture-behind-self-improving-ai-agents-full-implementation-guide-dfb0ce67c7c7)  
> 44. I made a stupid simple MAINTENANCE.md for AI docs and it actually fixed a bunch of nonsense - Reddit, accessed on March 21, 2026, [https://www.reddit.com/r/AI\_Agents/comments/1rxwqn8/i\_made\_a\_stupid\_simple\_maintenancemd\_for\_ai\_docs/](https://www.reddit.com/r/AI_Agents/comments/1rxwqn8/i_made_a_stupid_simple_maintenancemd_for_ai_docs/)  
> 45. The Best AI Agent Frameworks | Software Mansion, accessed on March 21, 2026, [https://blog.swmansion.com/the-best-ai-agent-frameworks-5aea3d8c5d93](https://blog.swmansion.com/the-best-ai-agent-frameworks-5aea3d8c5d93)  
> 46. crewAIInc/crewAI: Framework for orchestrating role-playing ... - GitHub, accessed on March 21, 2026, [https://github.com/crewaiinc/crewai](https://github.com/crewaiinc/crewai)  
> 47. Open-source AI agents - Modal, accessed on March 21, 2026, [https://modal.com/blog/open-ai-agents](https://modal.com/blog/open-ai-agents)  
> 48. Best terminal-based AI pair programmers in 2024 - Aider vs Plandex vs OpenHands : r/LocalLLaMA - Reddit, accessed on March 21, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1hok4h4/best\_terminalbased\_ai\_pair\_programmers\_in\_2024/](https://www.reddit.com/r/LocalLLaMA/comments/1hok4h4/best_terminalbased_ai_pair_programmers_in_2024/)  
> 49. Agentic AI Comparison: Aider vs OpenDevin, accessed on March 21, 2026, [https://aiagentstore.ai/compare-ai-agents/aider-vs-opendevin](https://aiagentstore.ai/compare-ai-agents/aider-vs-opendevin)  
> 50. Aider vs OpenHands: A Comprehensive Comparison - How to create an AI agent, accessed on March 21, 2026, [https://createaiagent.net/comparisons/aider-vs-openhands/](https://createaiagent.net/comparisons/aider-vs-openhands/)  
> 51. I built a locally-hosted AI agent that runs entirely on your own hardware no cloud, no subscriptions : r/ollama - Reddit, accessed on March 21, 2026, [https://www.reddit.com/r/ollama/comments/1rdyosq/i\_built\_a\_locallyhosted\_ai\_agent\_that\_runs/](https://www.reddit.com/r/ollama/comments/1rdyosq/i_built_a_locallyhosted_ai_agent_that_runs/)  
> 52. Unleashing LLMs with Team Red: Your Guide to AMD-Powered Local AI - Medium, accessed on March 21, 2026, [https://medium.com/data-science-collective/unleashing-llms-with-team-red-your-guide-to-amd-powered-local-ai-3be6ea745f0f](https://medium.com/data-science-collective/unleashing-llms-with-team-red-your-guide-to-amd-powered-local-ai-3be6ea745f0f)  
> 53. Sandboxing: Running LLM generated code in secure environment | by Sharath S Hebbar | Feb, 2026, accessed on March 21, 2026, [https://medium.com/@sharathhebbar24/sandboxing-running-llm-generated-code-in-secure-environment-392869c32c06](https://medium.com/@sharathhebbar24/sandboxing-running-llm-generated-code-in-secure-environment-392869c32c06)  
> 54. Chapter 3: Architectures for Building Agentic AI - arXiv.org, accessed on March 21, 2026, [https://arxiv.org/html/2512.09458v1](https://arxiv.org/html/2512.09458v1)  
> 55. Self-Evolving Agents - A Cookbook for Autonomous Agent Retraining - OpenAI Developers, accessed on March 21, 2026, [https://developers.openai.com/cookbook/examples/partners/self\_evolving\_agents/autonomous\_agent\_retraining](https://developers.openai.com/cookbook/examples/partners/self_evolving_agents/autonomous_agent_retraining)
