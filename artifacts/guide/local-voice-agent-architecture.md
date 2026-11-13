---
title: Local Voice Agent Architecture Plan
kind: guide
status: stable
summary: "Engineering blueprint for a fully local, low-latency bilingual (Polish/English) voice agent within 32 GB VRAM."
tags: [voice-agent, stt, tts, local-ai]
created: 2026-09-14
updated: 2026-09-14
origin: "https://docs.google.com/document/d/1z22ltOYEGoHzFhnF8XypQr4iy24WxNQaEXlJ39JZfGU"
---

# **Architecting a 100% Local, Low-Latency Voice Agent: A Comprehensive Engineering Blueprint**

## **Core Model Architecture**

The realization of a fully autonomous, local voice agent operating within a strict 32GB VRAM constraint demands a precise alignment of foundational models. The architecture must balance high-fidelity bilingual comprehension (Polish and English) with deterministic tool execution and ultra-low latency. Achieving natural conversational fluidity requires the orchestration of three optimized engines: a Large Language Model (LLM) serving as the cognitive routing core, a Speech-to-Text (STT) model for acoustic parsing, and a Text-to-Speech (TTS) model for vocal synthesis.

### **The Cognitive Engine: Large Language Model Selection**

The 32GB VRAM ceiling dictates that the central cognitive engine cannot exceed the 14-billion parameter threshold without introducing catastrophic latency via system RAM offloading1. For bilingual operations requiring sophisticated tool calling, the architectural optimum resides in the 7B to 11B parameter range.  
Evaluation of state-of-the-art weights positions the Bielik-11B v2.3 Instruct model as the optimal cognitive core for environments requiring native Polish and English fluency4. Built upon a depth-upscaled Mistral architecture and rigorously instruction-tuned on curated Polish and English corpora totaling over 10 billion tokens, Bielik-11B achieves exceptional performance on the Open PL LLM Leaderboard7. Scoring an impressive 65.71, the model rivals significantly larger 70B parameter models in linguistic comprehension and complex reasoning tasks6.  
While foundational models such as Qwen2.5-7B-Instruct exhibit robust function-calling capabilities and structured output adherence10, Bielik-11B v2.3 provides superior cross-lingual transfer, morphological accuracy, and contextual retrieval capabilities in Polish4. To fit within the designated memory budget while preserving reasoning fidelity, the model must be deployed using 4-bit quantization, specifically AWQ (Activation-aware Weight Quantization) or GPTQ formats. A 4-bit quantized 11B model occupies approximately 6.2GB of VRAM7, leaving the critical remaining memory for the context window (KV cache), STT, and TTS subsystems. This careful selection ensures that the system avoids swapping to system memory, preserving the real-time inference speed required for conversational agents.

### **The Vocal Tract: Text-to-Speech Synthesis**

A voice agent's perceived naturalness is heavily dependent on the Time-to-First-Audio (TTFA). The human conversational tolerance threshold is strictly bounded at approximately 800 milliseconds end-to-end15. Delays exceeding this margin severely disrupt the illusion of real-time interaction, often leading to user frustration and unintended interruptions15. Consequently, the TTS engine must synthesize audio almost instantaneously.  
Kokoro TTS emerges as the definitive solution for this constraint within a local environment. Operating with an extraordinarily lightweight architecture of 82 million parameters, Kokoro delivers a Real-Time Factor (RTF) of 0.03 while consuming less than 1GB of VRAM18. It is capable of delivering the first frame of audio in approximately 40 to 45 milliseconds on local GPU hardware21. Crucially, Kokoro supports multiple languages, including English and Polish, outputting 24 kHz audio with high naturalness (Mean Opinion Score of 4.2) that rivals proprietary cloud APIs without the crippling network latency penalty18.  
Alternative architectures, such as Fish Speech, offer excellent zero-shot cloning and multi-speaker capabilities but demand roughly 4GB of VRAM and operate at higher latency tiers (RTF 0.12)19. While highly expressive, dedicating 4GB of VRAM solely to synthesis severely constricts the LLM's available KV cache and jeopardizes the sub-second end-to-end requirement19. Kokoro’s flow-matching decoder with sway sampling ensures stable prosody while remaining invisible to the broader system’s resource allocation budget20.

### **The Orchestration Framework**

Integrating streaming STT, LLM inference, local tool execution, and TTS requires a highly specialized orchestration layer. The architectural comparison between Pipecat, LiveKit Agents, and AnovaX reveals divergent design philosophies that dictate their suitability for a homelab environment.  
LiveKit is an infrastructure-first framework built heavily around WebRTC rooms, optimized for scaling voice AI across distributed networks and telephony endpoints26. In LiveKit, the agent joins a room as a headless participant reacting to events27. Conversely, Pipecat is a pipeline-first framework built around a directed graph of composable processors27. Data moves through this pipeline as strictly typed "Frames" (such as AudioRawFrame, TextFrame, and TranscriptionFrame)28.  
AnovaX offers a compelling reference architecture for local-first execution, utilizing a multi-agent orchestrator that translates JSON plans into typed child agents on a bounded thread pool32. However, AnovaX relies heavily on cloud dependencies for ASR and planning, and its screen-content perception limitations make it fragile for low-level system integration without extensive modification32.  
For a locally hosted, bare-metal deployment prioritizing deep operating system integration and custom shell access, Pipecat is the superior architectural choice27. Its pipeline allows for deterministic, granular control over local Python processes. A Pipecat directed graph enables the asynchronous execution of safety filters, custom code sandboxes, and hardware-accelerated audio routing without the overhead of maintaining local WebRTC Selective Forwarding Unit (SFU) infrastructure26. The ability to fork the pipeline to execute Python-based whitelist/denylist validators on incoming TextFrame outputs from the LLM makes Pipecat uniquely suited for executing local shell actions26.

## **Execution Procedure and Resource Allocation**

Deploying a complex, multi-model voice pipeline onto a single 32GB GPU requires mathematical precision in memory allocation. Failure to strictly boundary these models results in the Linux Out-Of-Memory (OOM) killer terminating the processes during context spikes, leading to system failure during critical conversational turns.

### **VRAM Topology and Memory Partitioning**

The deployment stack relies on vLLM as the high-throughput inference server for the cognitive engine. By default, vLLM assumes full ownership of the GPU, aggressively pre-allocating a contiguous memory block based on the gpu\_memory\_utilization parameter, which defaults to 0.902. If initiated with default settings, vLLM will reserve approximately 28.8GB of the 32GB pool, immediately starving the subsequent STT and TTS processes and causing fatal initialization crashes2.  
To stabilize the cluster, hardware resources must be explicitly partitioned to accommodate the entire pipeline.

| Subsystem | Model / Configuration | Peak VRAM Footprint | Description |
| :---- | :---- | :---- | :---- |
| **STT Engine** | Faster-Whisper large-v3-turbo (INT8) | ~1.6 GB | Acoustic parsing and low-latency streaming transcription38. |
| **TTS Engine** | Kokoro TTS 82M | ~1.0 GB | Real-time speech synthesis19. |
| **LLM Weights** | Bielik-11B v2.3 Instruct (AWQ 4-bit) | ~6.2 GB | Static weight allocation7. |
| **System Overhead** | CUDA Context & PyTorch Allocator | ~2.0 GB | Baseline operational footprint and overhead36. |
| **KV Cache Pool** | vLLM PagedAttention | ~18.0 GB | Dynamic memory blocks for interaction history2. |
| **Total Target** |  | **~28.8 GB** | Maintains a safe margin below the 32GB physical limit. |

To enforce this topology, the vLLM engine must be initialized with a carefully tuned gpu\_memory\_utilization flag. Setting this value to 0.60 restricts vLLM's total physical claim to approximately 19.2GB, which effortlessly encompasses both the 6.2GB model weights and roughly 13GB of dynamic KV cache2. This explicit limitation guarantees that the remaining VRAM is protected for the audio processing models and prevents memory fragmentation from colliding with the display server or sibling CUDA processes2.  
Furthermore, the max\_model\_len parameter must be aggressively clamped to 8192 tokens. Accepting a model's theoretical maximum context length (e.g., 32k or 128k) forces the engine to dimension its PagedAttention blocks for an unused ceiling, wasting gigabytes of VRAM on unutilized pre-allocation2. Capping the model length directly aligns the memory footprint with realistic conversational history limits.

### **The Execution Sandbox**

Granting an LLM direct shell and file access introduces severe security vulnerabilities. Malformed logic, prompt injection attacks, or simple hallucinations could result in destructive operations. Relying on basic Python wrappers like subprocess.run alongside an Abstract Syntax Tree (AST) validator (e.g., safepyrun) provides useful logic checks but zero actual process isolation; the command ultimately runs with the full privileges of the host process42.  
The industry-standard mechanism for unprivileged, zero-trust tool execution is Bubblewrap (bwrap). Bubblewrap is a low-level, setuid-free sandboxing tool that utilizes Linux user, mount, and PID namespaces to isolate processes directly at the kernel level45. When the LLM emits a valid JSON tool call intended for the shell, the orchestrator must spawn a bwrap process rather than a direct shell subprocess.  
A secure invocation pattern ensures the agent has access only to the explicit resources required for the task. The sandbox is constructed using strict command-line arguments that systematically revoke access to the host machine:  
The --unshare-all flag is fundamental; it severs all namespace connections to the host, including network, Inter-Process Communication (IPC), and UNIX Time-Sharing (UTS) boundaries45. If the agent attempts to exfiltrate data or download malicious payloads, the absence of a network interface renders the operation impossible45. To allow the agent to utilize basic commands, --ro-bind /usr /usr and --ro-bind /lib /lib mount essential system binaries and libraries in strictly read-only mode, preventing the permanent modification of the host operating system46.  
For data manipulation, --bind /path/to/agent/workspace /workspace provides a highly scoped, read-write directory. The agent can freely read, write, and execute scripts within this isolated folder without exposing the broader host filesystem48. Finally, the --seccomp argument injects a Berkeley Packet Filter (BPF) to explicitly deny dangerous system calls, such as mount, ptrace, or reboot. This neutralizes privilege escalation attempts even from within the namespace boundary47.  
By default, Bubblewrap grants zero access48. If the LLM generates a command targeting /etc/shadow or attempts to traverse outside its designated workspace, the Linux kernel enforces an immediate Operation not permitted error48, ensuring the underlying homelab infrastructure remains entirely insulated from the agent's autonomous actions.

### **Streaming Pipeline Sequencing**

To achieve the sub-second latency required for natural conversation, sequential batch processing must be abandoned entirely in favor of a highly concurrent streaming architecture15. Traditional pipelines that wait for the user to finish speaking, transcribe the entire audio, generate the complete text response, and finally synthesize the audio inherently generate latencies exceeding several seconds, breaking the conversational illusion17.  
The optimized pipeline initiates with Voice Activity Detection (VAD). The Silero VAD model is deployed to monitor the incoming audio stream continuously in 30-millisecond frames55. Upon detecting an utterance, the audio is buffered until a predefined silence threshold is reached. Setting this threshold requires precise calibration; approximately 300 milliseconds is the optimal balance between rapid response and avoiding premature interruptions during natural human speech pauses55.  
Once Silero signals the end-of-utterance, the audio chunk is routed to Faster-Whisper. Faster-Whisper is a production-grade reimplementation of the OpenAI Whisper architecture utilizing the CTranslate2 engine59. Leveraging layer fusion and INT8 precision, it transcribes the audio highly efficiently, often completing inference on a standard utterance in under 100 to 150 milliseconds without compromising Word Error Rate (WER)38.  
The transcription is immediately injected into the Pipecat pipeline, which streams the text to the vLLM server via an OpenAI-compatible API. As the LLM autoregressively generates the response, tokens are not held until completion. Instead, they are routed through Pipecat's text aggregation processors28. Once a logical chunk of text is formed, it is passed directly to the Kokoro TTS engine. Kokoro synthesizes the audio incrementally, allowing playback to begin while the LLM is still generating subsequent tokens16. This aggressively overlapped execution—hiding the LLM generation time behind the initial TTS synthesis—is the singular methodology for achieving an end-to-end Time-to-First-Audio under 800 milliseconds15.

## **Heuristics and Latency Trade-Offs**

The architecture of a local voice agent requires nuanced engineering decisions, balancing the raw speed of computation against the qualitative feel of the acoustic interaction.

### **Tooling Depth vs. Transport Breadth**

The selection of the orchestration framework hinges on the primary domain of operation. LiveKit Agents abstracts away network instability, NAT traversal, and SIP trunking through a robust WebRTC implementation, making it unparalleled for remote, web-facing deployments scaling to thousands of concurrent users27. However, when the agent is acting as a local homelab assistant, global network transport and massive concurrency are not the primary engineering bottlenecks.  
Pipecat is prioritized because it permits deep, untethered integration into the host environment. By treating audio, text, and control structures as arbitrary "Frames" traversing a pipeline, Pipecat allows developers to insert custom Python logic between any two nodes seamlessly27. This architecture facilitates the interception of LLM text frames, parsing them for custom tool signatures, halting the audio pipeline, triggering the Bubblewrap sandbox, and injecting the terminal output back into the conversation graph as a new context frame without relying on external cloud APIs26.

### **TTS Chunking Strategy**

In streaming voice pipelines, the granularity at which text is sent to the TTS engine profoundly impacts both operational latency and acoustic quality. Sending individual words minimizes initial latency but forces the TTS model to synthesize audio without context. This destroys the natural cadence, pitch, and intonation (prosody) of the generated voice, resulting in a robotic and disjointed output62.  
For highly inflected languages like Polish, context-blind synthesis is particularly destructive. Polish grammar relies on complex declensions and context-dependent pronunciations; therefore, sentence-level context is mandatory to generate the correct acoustic stress and flow31.  
The optimal heuristic utilizes Pipecat's SentenceAggregator31. This module employs natural language toolkits and regular expressions to buffer the streaming LLM tokens until a definitive sentence boundary (e.g., a period, exclamation point, or question mark followed by whitespace) is detected31. While waiting for a complete sentence adds marginal latency—equivalent to the time required for the LLM to generate roughly 10 to 15 tokens—the resulting prosodic quality from Kokoro TTS is significantly elevated31. At response lengths exceeding standard conversational turns, the performance gain of word-level chunking no longer justifies the severe degradation in audio naturalness, validating the sentence-level aggregation trade-off.

## **Failure Modes and Resilience**

A robust voice agent must be engineered to withstand the stochastic nature of generative models and the physical realities of real-time audio routing.

### **Safety Filters and Constrained Decoding**

A persistent failure mode in tool-capable LLMs is the generation of malformed JSON structures or hallucinations of non-existent arguments. When interacting with shell environments, parsing errors inevitably crash the execution loop, stalling the agent66. Relying solely on prompt engineering to enforce schema compliance is insufficient for production stability, as models frequently inject conversational prose alongside structured data67.  
To guarantee structural validity without incurring massive latency penalties, the vLLM engine must be configured to utilize constrained decoding algorithms. When a tool is invoked, a strict JSON schema derived from Pydantic models is passed to the engine. Historically, the outlines backend was utilized for this purpose, but it introduces severe CPU-GPU synchronization overhead and compilation delays, significantly degrading Time-to-First-Token (TTFT) metrics69.  
The modern architectural solution is to configure vLLM with the xgrammar guided decoding backend69. The xgrammar implementation compiles the JSON schema into a highly efficient finite state machine, limiting the token selection probability distribution only to valid syntax characters at any given step during generation66. This mathematical constraint guarantees that even when the user switches contextually from English to Polish mid-command, the generated tool payload remains perfectly structured, bypassing the need for complex, latency-heavy fallback loops or prompt-based repair attempts67.

### **Audio Routing and Acoustic Echo Cancellation**

A pervasive physical failure mode in voice agents is the acoustic feedback loop: the local microphone captures the agent's own TTS output from the speakers, feeding it back into the STT engine71. This causes the LLM to interpret its own synthesis as user input, leading to an endless cycle of self-replying and rapid resource exhaustion.  
While hardware-based Acoustic Echo Cancellation (AEC) via dedicated Digital Signal Processors provides the cleanest signal separation, software-based AEC is mandatory for homelab environments using standard peripherals. The Linux audio server PipeWire provides a robust, low-latency module, libpipewire-module-echo-cancel, which leverages WebRTC AEC algorithms natively at the OS level71.  
By configuring this module, PipeWire creates a virtual source and sink. It actively correlates the playback stream (the TTS output routed to the speakers) with the capture stream (the microphone input), mathematically subtracting the synthesized audio from the microphone signal before it reaches the Silero VAD71. This isolates the user's true voice and prevents catastrophic feedback cascades without requiring proprietary conferencing software.

### **VRAM Fragmentation and Cache Lifecycle**

Long-running autonomous sessions naturally accumulate vast amounts of dialogue history, steadily expanding the required KV cache. If improperly managed, this leads to severe memory fragmentation, ultimately triggering CUDA Out-Of-Memory exceptions hours or days after the initial deployment74.  
vLLM's PagedAttention architecture mitigates traditional external fragmentation by treating GPU memory analogously to operating system virtual memory, breaking the KV cache into fixed-size, non-contiguous blocks allocated on demand74. To further optimize resources during prolonged operation, enabling enable\_prefix\_caching=True allows the engine to recognize shared system prompts and tool definitions across multiple conversational turns, storing them once and sharing the memory pointers across requests79.  
However, high-concurrency environments or continuous interactions with long shared prefixes can still encounter severe performance degradation or memory leaks related to garbage collection and block pool exhaustion82. To stabilize the runtime, the cluster must implement chunked prefill (enable\_chunked\_prefill=True) alongside a carefully configured max\_num\_batched\_tokens limit (e.g., 4096)85. This ensures that the compute budget per scheduler step does not overwhelm the active memory blocks, decoupling the prefill phase from the decode phase and maintaining stable throughput over extended uptimes without triggering memory leaks85.

## **Architectural Anti-Patterns**

Certain design philosophies that excel in text-based environments become critical bottlenecks when translated to the stringent latency requirements of real-time voice architectures.

### **The 14B+ Parameter Trap**

A common anti-pattern among homelab engineers is attempting to deploy the largest possible LLM (e.g., 14B to 32B parameters) within a constrained 32GB environment. While it is technically feasible to force a 32B model into 32GB of VRAM using aggressive quantization, doing so eliminates the crucial memory headroom required for the dynamic KV cache and the concurrent operation of the STT and TTS models3.  
The KV cache footprint is inescapable and scales linearly with context length; the mathematical reality is absolute: Cache Bytes = 2 × Layers × Heads × Head\_Dimension × Sequence\_Length × Precision39. When VRAM is exhausted by static model weights, the inference engine attempts to spill the KV tensors into system RAM to avoid crashing. Communicating over the PCIe bus introduces massive latency penalties. Token generation speed drops from an acceptable ~40 tokens per second to low single digits, destroying the voice pipeline's RTF and rendering the conversational experience entirely unusable1. For real-time voice agents, a highly capable 11B model (like Bielik) residing entirely within rapid GPU memory will invariably outperform a 32B model bottlenecked by PCIe offloading.

### **The Static Intent Dead End**

Historically, home automation frameworks like Home Assistant have relied heavily on rigid Intent systems. These systems map specific voice phrases to predefined actions using deterministic regular expressions or narrow classification models32.  
Attempting to build a fluid, intelligent agent strictly on top of these static intents is an architectural dead end. Real-time voice agents aim for dynamic planning, context-aware reasoning, and iterative problem-solving. If a user issues a complex command such as, "Read the system logs and if the CPU is hot, throttle the container," a static intent parser will fail entirely. The architecture must embrace true LLM-driven tool calling, where the agent is provided with an API schema (enforced via xgrammar) and autonomously synthesizes the necessary parameters and execution sequence to fulfill the intent dynamically32.

## **Quality Signals and Telemetry**

Ensuring the system remains responsive and accurate requires defining and monitoring strict quantitative thresholds across the entire pipeline.

### **Hard Latency Metrics**

The performance of the voice pipeline must be continually evaluated against the 800ms human tolerance boundary15.

| Metric | Definition | Target Threshold |
| :---- | :---- | :---- |
| **VAD Endpointing** | Silence duration required to assume speech completion. | ~300 ms56 |
| **STT RTF** | Real-Time Factor of the Faster-Whisper transcription. | \< 0.1020 |
| **LLM TTFT** | Time to First Token generated by the vLLM engine. | \< 300 ms29 |
| **LLM ITL** | Inter-Token Latency governing generation speed. | \< 40 ms29 |
| **TTS TTFA** | Time to First Audio synthesized by Kokoro. | \< 100 ms22 |

If system logs indicate that TTFT consistently exceeds 300ms or that STT processing is bottlenecking the pipeline, it serves as an immediate signal of underlying resource starvation, prompt overloading, or suboptimal quantization choices56. Maintaining these metrics ensures that the sum of processing delays remains hidden behind the natural rhythm of conversation.

### **Syntax and Translation Validation**

For bilingual agents operating in English and Polish, a critical quality signal is the precision of tool invocation when the linguistic context shifts unexpectedly. If a user dictates a command in Polish, the LLM must seamlessly map the intent to the expected English tool parameters defined in the backend codebase.  
The validity of the returned tool structure must be heavily logged at the orchestration layer. Any instance of the LLM attempting to translate the JSON keys into Polish (e.g., outputting {"nazwa\_pliku": "log.txt"} instead of the expected {"filename": "log.txt"}) signifies a failure in the prompt alignment or an absence of strict xgrammar enforcement66. Telemetry must capture these validation errors to refine the JSON schemas and ensure the model prioritizes structural integrity over linguistic continuation67.

## **Operational Boundaries**

The desire to provide the agent with vast contextual awareness conflicts directly with the physics of memory allocation and processing latency, necessitating strict operational boundaries.

### **RAG Context Limits**

Retrieval-Augmented Generation (RAG) is commonly utilized to ground agents in external knowledge bases or extensive system logs. However, as previously established, the KV cache footprint scales linearly with context length39.  
For an 11B parameter model, expanding the context window to accommodate 32,000 tokens of attached documents consumes massive amounts of VRAM per request40. More detrimentally, the initial prefill phase required to compute the attention mechanisms over those tens of thousands of tokens requires immense computational power. Even with modern optimizations, ingesting a massive context block spikes the TTFT well beyond the 800ms conversational budget, causing the agent to pause unnaturally for several seconds before responding15.  
Consequently, the operational boundary for a real-time voice agent dictates strict RAG truncation. Context injection must be heavily curtailed, delivering only the top-k most mathematically relevant chunks from the vector database. In a low-latency environment, preserving the instantaneous fluidity of the conversation takes absolute precedence over encyclopedic context ingestion.

#### **Works cited**

> 1. unsloth/Mistral-Small-24B-Instruct-2501-unsloth-bnb-4bit, [https://huggingface.co/unsloth/Mistral-Small-24B-Instruct-2501-unsloth-bnb-4bit](https://huggingface.co/unsloth/Mistral-Small-24B-Instruct-2501-unsloth-bnb-4bit)  
> 2. How to fix vLLM OOM: the complete 2026 checklist - Sector88, [https://www.sector88.co/blog/how-to-fix-vllm-oom](https://www.sector88.co/blog/how-to-fix-vllm-oom)  
> 3. KV cache in VLLM serve \#17054 - GitHub, [https://github.com/vllm-project/vllm/discussions/17054](https://github.com/vllm-project/vllm/discussions/17054)  
> 4. European Leaderboard Bielik - a Hugging Face Space by speakleash, [https://huggingface.co/spaces/speakleash/european\_leaderboard\_bielik](https://huggingface.co/spaces/speakleash/european_leaderboard_bielik)  
> 5. speakleash/Bielik-11B-v2 - Hugging Face, [https://huggingface.co/speakleash/Bielik-11B-v2](https://huggingface.co/speakleash/Bielik-11B-v2)  
> 6. aleksanderobuchowski/bielik-11b-v2.3-instruct - Replicate, [https://replicate.com/aleksanderobuchowski/bielik-11b-v2.3-instruct](https://replicate.com/aleksanderobuchowski/bielik-11b-v2.3-instruct)  
> 7. Bielik-11B-v2.2-Instruct vs Bielik-7B-v0.1 - AIModels.fyi, [https://www.aimodels.fyi/models/compare/bielik-11b-v2.2-instruct-speakleash-vs-bielik-7b-v01-speakleash](https://www.aimodels.fyi/models/compare/bielik-11b-v2.2-instruct-speakleash-vs-bielik-7b-v01-speakleash)  
> 8. Bielik 11B v2 Technical Report - arXiv, [https://arxiv.org/html/2505.02410v2](https://arxiv.org/html/2505.02410v2)  
> 9. Bielik 11B v2 Technical Report - arXiv, [https://arxiv.org/pdf/2505.02410](https://arxiv.org/pdf/2505.02410)  
> 10. Knowledge is Not Enough: Injecting RL Skills for Continual Adaptation, [https://arxiv.org/html/2601.11258v1](https://arxiv.org/html/2601.11258v1)  
> 11. GUIDE: Towards Scalable Advising for Research Ideas, [https://aclanthology.org/2026.acl-long.1984.pdf](https://aclanthology.org/2026.acl-long.1984.pdf)  
> 12. Invocation Refiner: A Plug-and-Play Module for Rectifying LLM Tool, [https://aclanthology.org/2026.findings-acl.731.pdf](https://aclanthology.org/2026.findings-acl.731.pdf)  
> 13. Bielik 11B v2 Technical Report - arXiv, [https://arxiv.org/html/2505.02410v1](https://arxiv.org/html/2505.02410v1)  
> 14. Bielik 11B V2.2 Instruct AWQ by speakleash — VRAM 6.2GB, 32K, [https://llm-explorer.com/model/speakleash%2FBielik-11B-v2.2-Instruct-AWQ,7CnuNH97YpU7LvtDFeLjK0](https://llm-explorer.com/model/speakleash%2FBielik-11B-v2.2-Instruct-AWQ,7CnuNH97YpU7LvtDFeLjK0)  
> 15. The Voice Agent Latency Budget: A Practical Breakdown with Real, [https://actioneer-1.ghost.io/voice-agent-context-retrieval-latency-budget/](https://actioneer-1.ghost.io/voice-agent-context-retrieval-latency-budget/)  
> 16. A Deep Dive into Voice Agent Architectures and Best Practices, [https://huggingface.co/blog/abdeljalilELmajjodi/deep-dive-into-voice-agent](https://huggingface.co/blog/abdeljalilELmajjodi/deep-dive-into-voice-agent)  
> 17. Architecting Low-Latency, Real-Time AI Voice Agents, [https://dev.to/lifeisverygood/architecting-low-latency-real-time-ai-voice-agents-challenges-solutions-hdn](https://dev.to/lifeisverygood/architecting-low-latency-real-time-ai-voice-agents-challenges-solutions-hdn)  
> 18. Text To Speech - Models | Machine Learning Inference | DeepInfra, [https://deepinfra.com/models/text-to-speech](https://deepinfra.com/models/text-to-speech)  
> 19. Best TTS Models 2026: Open-Source Voice AI Compared | CodeSOTA, [https://www.codesota.com/guides/tts-models](https://www.codesota.com/guides/tts-models)  
> 20. OmniVoice Review: Real Voice Cloning & Multilingual TTS, [https://omnivoice.app/blog/omnivoice-voice-cloning-review](https://omnivoice.app/blog/omnivoice-voice-cloning-review)  
> 21. ElevenLabs vs Self-Hosted TTS: Voice Quality Comparison GIGAGPU, [https://gigagpu.com/elevenlabs-vs-self-hosted-tts-quality/](https://gigagpu.com/elevenlabs-vs-self-hosted-tts-quality/)  
> 22. Best FREE ElevenLabs Alternatives & Opensource Text to Speech, [https://nerdynav.com/open-source-ai-voice/](https://nerdynav.com/open-source-ai-voice/)  
> 23. Fish Audio S2.1 Pro: Free Text-to-Speech API for Developers, [https://fish.audio/blog/s2-1-pro-free-api/](https://fish.audio/blog/s2-1-pro-free-api/)  
> 24. Fish Audio Reviews 2026: Details, Pricing, & Features - G2, [https://www.g2.com/products/fish-audio/reviews](https://www.g2.com/products/fish-audio/reviews)  
> 25. \[Second Take\] Kokoro-82M is an Apache TTS model - Reddit, [https://www.reddit.com/r/LocalLLaMA/comments/1hwf4jm/second\_take\_kokoro82m\_is\_an\_apache\_tts\_model/](https://www.reddit.com/r/LocalLLaMA/comments/1hwf4jm/second_take_kokoro82m_is_an_apache_tts_model/)  
> 26. Pipecat vs LiveKit: the trade-offs that lock you in | Chanl Blog, [https://www.channel.tel/blog/pipecat-vs-livekit-voice-framework-decision](https://www.channel.tel/blog/pipecat-vs-livekit-voice-framework-decision)  
> 27. Pipecat vs LiveKit Agents: Which Voice Framework to Build On, [https://www.dograh.com/feeds/blog/pipecat-livekit-agents](https://www.dograh.com/feeds/blog/pipecat-livekit-agents)  
> 28. LiveKit vs Pipecat: Which Voice AI Framework Should You Build On, [https://www.autointerviewai.com/blog/livekit-vs-pipecat-voice-ai-framework-2026](https://www.autointerviewai.com/blog/livekit-vs-pipecat-voice-ai-framework-2026)  
> 29. Building Enterprise Realtime Voice Agents from Scratch - arXiv, [https://arxiv.org/html/2603.05413v2](https://arxiv.org/html/2603.05413v2)  
> 30. RoomKit, Pipecat, TEN Framework, LiveKit Agents, [https://www.roomkit.live/blog/choosing-the-right-conversational-ai-framework/](https://www.roomkit.live/blog/choosing-the-right-conversational-ai-framework/)  
> 31. sentence — pipecat-ai documentation, [https://reference-server.pipecat.ai/en/latest/api/pipecat.processors.aggregators.sentence.html](https://reference-server.pipecat.ai/en/latest/api/pipecat.processors.aggregators.sentence.html)  
> 32. AnovaX: A Local, Multi-Agent Voice Assistant with LLM Planning, [https://arxiv.org/html/2607.15367v1](https://arxiv.org/html/2607.15367v1)  
> 33. The 10 Best AI Voice Tools (August 2026): Features, Use Cases, [https://mastra.ai/articles/best-ai-voice-tools](https://mastra.ai/articles/best-ai-voice-tools)  
> 34. Building an Open Source Voice AI Agent That Avoids Vendor Lock-In, [https://webrtc.ventures/2026/02/building-an-open-source-voice-ai-agent-that-avoids-vendor-lock-in/](https://webrtc.ventures/2026/02/building-an-open-source-voice-ai-agent-that-avoids-vendor-lock-in/)  
> 35. Source code for pipecat.utils.text.pattern\_pair\_aggregator, [https://reference-server.pipecat.ai/en/latest/\_modules/pipecat/utils/text/pattern\_pair\_aggregator.html](https://reference-server.pipecat.ai/en/latest/_modules/pipecat/utils/text/pattern_pair_aggregator.html)  
> 36. Fixing vLLM CUDA Out of Memory: KV Cache Tuning Guide - Lyceum, [https://lyceum.technology/magazine/fixing-vllm-cuda-out-of-memory-kv-cache/](https://lyceum.technology/magazine/fixing-vllm-cuda-out-of-memory-kv-cache/)  
> 37. Possible GPU Memory Utilization issue/bug for embeddings model, [https://github.com/vllm-project/vllm/issues/12308](https://github.com/vllm-project/vllm/issues/12308)  
> 38. Self-Host Faster-Whisper on GPU Cloud: Production Deployment, [https://www.spheron.network/blog/faster-whisper-gpu-cloud-production-deployment-guide/](https://www.spheron.network/blog/faster-whisper-gpu-cloud-production-deployment-guide/)  
> 39. LLM KV Cache Memory Calculator (per token, sequence & GPU), [https://induwara.lk/tools/ai-kv-cache-calculator](https://induwara.lk/tools/ai-kv-cache-calculator)  
> 40. KV Cache Memory Calculation for LLMs | Technical Guide - Lyceum, [https://lyceum.technology/magazine/kv-cache-memory-calculation-llm/](https://lyceum.technology/magazine/kv-cache-memory-calculation-llm/)  
> 41. Fix vLLM CUDA Out of Memory at Startup: KV Cache + Docker, [https://subodhkc.com/blog/fix-vllm-cuda-out-of-memory-kv-cache-docker](https://subodhkc.com/blog/fix-vllm-cuda-out-of-memory-kv-cache-docker)  
> 42. safepyrun - PyPI, [https://pypi.org/project/safepyrun/](https://pypi.org/project/safepyrun/)  
> 43. Build a Coding Agent in Python: Step-by-Step Tutorial - Sid Bharath, [https://sidbharath.com/blog/build-a-coding-agent-python-tutorial/](https://sidbharath.com/blog/build-a-coding-agent-python-tutorial/)  
> 44. Safely Running Untrusted Code: A Hands-On Guide to Google, [https://medium.com/google-cloud/safely-running-untrusted-code-a-hands-on-guide-to-google-cloud-run-sandboxes-8bbc95d391c7](https://medium.com/google-cloud/safely-running-untrusted-code-a-hands-on-guide-to-google-cloud-run-sandboxes-8bbc95d391c7)  
> 45. Want Sandbox as Non-Root? bwrap Unprivileged ... - X-CMD, [https://www.x-cmd.com/install/bwrap/](https://www.x-cmd.com/install/bwrap/)  
> 46. containers/bubblewrap: Low-level unprivileged sandboxing ... - GitHub, [https://github.com/containers/bubblewrap](https://github.com/containers/bubblewrap)  
> 47. Bubblewrap - ArchWiki, [https://wiki.archlinux.org/title/Bubblewrap](https://wiki.archlinux.org/title/Bubblewrap)  
> 48. Coding Agents V: Why Bubblewrap Wraps Agents - Palaimon GmbH, [https://palaimon.io/blog/coding-agents-bubblewrap-deep-dive/](https://palaimon.io/blog/coding-agents-bubblewrap-deep-dive/)  
> 49. Sandboxing AI agents on Upsun, [https://developer.upsun.com/posts/tutorials/sandboxing-ai-agents-on-upsun](https://developer.upsun.com/posts/tutorials/sandboxing-ai-agents-on-upsun)  
> 50. Bubblewrap Without the Pain - Smaller Fish, [https://smaller.fish/posts/bubblewrap\_tui](https://smaller.fish/posts/bubblewrap_tui)  
> 51. Easy Sandboxing on Linux with Bubblewrap, [https://bxt.rs/blog/easy-sandboxing-on-linux-with-bubblewrap/](https://bxt.rs/blog/easy-sandboxing-on-linux-with-bubblewrap/)  
> 52. How to configure seccomp profiles for syscall filtering - OneUptime, [https://oneuptime.com/blog/post/2026-02-09-seccomp-profiles-syscall-filtering/view](https://oneuptime.com/blog/post/2026-02-09-seccomp-profiles-syscall-filtering/view)  
> 53. How Loka Built a Natural, Low-Latency Voice Agent with Amazon, [https://aws.amazon.com/blogs/machine-learning/how-loka-built-a-natural-low-latency-voice-agent-with-amazon-nova-2-sonic/](https://aws.amazon.com/blogs/machine-learning/how-loka-built-a-natural-low-latency-voice-agent-with-amazon-nova-2-sonic/)  
> 54. How to Build a Voice Agent System with AI Agents - Stackademic, [https://blog.stackademic.com/how-to-build-a-voice-agent-system-with-ai-agents-from-realtime-speech-to-production-ready-51d2d469a554](https://blog.stackademic.com/how-to-build-a-voice-agent-system-with-ai-agents-from-realtime-speech-to-production-ready-51d2d469a554)  
> 55. GitHub - gkonovalov/android-vad: Android Voice Activity Detection, [https://github.com/gkonovalov/android-vad](https://github.com/gkonovalov/android-vad)  
> 56. Voice AI Latency: Where It Comes From and How to Reduce It, [https://switchboard.audio/hub/voice-ai-latency/](https://switchboard.audio/hub/voice-ai-latency/)  
> 57. 2025 Voice AI Guide: How to Make Your Own Real-Time Voice, [https://programmerraja.is-a.dev/post/2025/2025-Voice-AI-Guide-How-to-Make-Your-Own-Real-Time-Voice-Agent-(Part-1)](https://programmerraja.is-a.dev/post/2025/2025-Voice-AI-Guide-How-to-Make-Your-Own-Real-Time-Voice-Agent-(Part-1))  
> 58. Building a Voice Bot from Scratch: Open Source Complete Guide, [https://www.dograh.com/feeds/blog/voice-bot-development](https://www.dograh.com/feeds/blog/voice-bot-development)  
> 59. Faster-Whisper: Install and Run 4x Faster Speech-to-Text, [https://localaimaster.com/blog/faster-whisper-guide](https://localaimaster.com/blog/faster-whisper-guide)  
> 60. Faster Whisper transcription with CTranslate2 - GitHub, [https://github.com/SYSTRAN/faster-whisper](https://github.com/SYSTRAN/faster-whisper)  
> 61. Event-Driven Voice-to-Voice Architecture for AI Agents - Donna, [https://www.askdonna.com/blog/how-to-built-an-event-driven-voice-to-voice-architecture-for-low-latency-ai-agents](https://www.askdonna.com/blog/how-to-built-an-event-driven-voice-to-voice-architecture-for-low-latency-ai-agents)  
> 62. PatternPairAggregator - Pipecat Documentation, [https://docs.pipecat.ai/api-reference/server/utilities/text/pattern-pair-aggregator](https://docs.pipecat.ai/api-reference/server/utilities/text/pattern-pair-aggregator)  
> 63. simple\_text\_aggregator — pipecat-ai documentation, [https://reference-server.pipecat.ai/en/latest/api/pipecat.utils.text.simple\_text\_aggregator.html](https://reference-server.pipecat.ai/en/latest/api/pipecat.utils.text.simple_text_aggregator.html)  
> 64. pipecat/examples/getting-started/04-sync-speech-and-image.py at, [https://github.com/pipecat-ai/pipecat/blob/main/examples/getting-started/04-sync-speech-and-image.py](https://github.com/pipecat-ai/pipecat/blob/main/examples/getting-started/04-sync-speech-and-image.py)  
> 65. string — pipecat-ai documentation, [https://reference-server.pipecat.ai/en/latest/api/pipecat.utils.string.html](https://reference-server.pipecat.ai/en/latest/api/pipecat.utils.string.html)  
> 66. constrained decoding, grammars, token masking - Belgavi's AI Lab, [https://aicassindra.com/blogs/llm/llm\_structured\_output.html](https://aicassindra.com/blogs/llm/llm_structured_output.html)  
> 67. LLM Structured Outputs: Schema Validation for Real Pipelines (2026), [https://collinwilkins.com/articles/structured-output](https://collinwilkins.com/articles/structured-output)  
> 68. Structured outputs: JSON Schema, OpenAI, Claude, Gemini | Logic, [https://logic.inc/resources/structured-outputs-guide](https://logic.inc/resources/structured-outputs-guide)  
> 69. General questions on structured output backend - vLLM Forums, [https://discuss.vllm.ai/t/general-questions-on-structured-output-backend/1444](https://discuss.vllm.ai/t/general-questions-on-structured-output-backend/1444)  
> 70. \[Bug\]: Very slow guided decoding with Outlines backend since v0, [https://github.com/vllm-project/vllm/issues/12005](https://github.com/vllm-project/vllm/issues/12005)  
> 71. module-echo-cancel.c - GitLab, [https://gitlab.retis.santannapisa.it/a.napolitano/pipewire/-/blob/cc7439187f61dd73b81ca69f5dbccbb52ce970b2/src/modules/module-echo-cancel.c](https://gitlab.retis.santannapisa.it/a.napolitano/pipewire/-/blob/cc7439187f61dd73b81ca69f5dbccbb52ce970b2/src/modules/module-echo-cancel.c)  
> 72. Echo Cancel - PipeWire, [https://docs.pipewire.org/page\_module\_echo\_cancel.html](https://docs.pipewire.org/page_module_echo_cancel.html)  
> 73. PipeWire/Examples - ArchWiki, [https://wiki.archlinux.org/title/PipeWire/Examples](https://wiki.archlinux.org/title/PipeWire/Examples)  
> 74. vAttention: Dynamic Memory Management for Serving LLMs without, [https://arxiv.org/html/2405.04437v3](https://arxiv.org/html/2405.04437v3)  
> 75. vLLM: How PagedAttention Solved the Memory Crisis in LLM Serving, [https://medium.com/@serahabensour/vllm-how-pagedattention-solved-the-memory-crisis-in-llm-serving-61aece9fdf83](https://medium.com/@serahabensour/vllm-how-pagedattention-solved-the-memory-crisis-in-llm-serving-61aece9fdf83)  
> 76. PagedAttention: Navigating VRAM Fragmentation - DEV Community, [https://dev.to/unitbuilds\_cc/pagedattention-navigating-vram-fragmentation-3521](https://dev.to/unitbuilds_cc/pagedattention-navigating-vram-fragmentation-3521)  
> 77. Introduction to vLLM and PagedAttention - Runpod, [https://www.runpod.io/blog/introduction-to-vllm-and-pagedattention](https://www.runpod.io/blog/introduction-to-vllm-and-pagedattention)  
> 78. vLLM Explained: PagedAttention and Continuous Batching - Runpod, [https://www.runpod.io/articles/guides/vllm-pagedattention-continuous-batching](https://www.runpod.io/articles/guides/vllm-pagedattention-continuous-batching)  
> 79. KV-Cache Wins You Can See: From Prefix Caching in vLLM ... - llm-d, [https://llm-d.ai/blog/kvcache-wins-you-can-see](https://llm-d.ai/blog/kvcache-wins-you-can-see)  
> 80. vLLM Optimization Techniques: 5 Practical Methods to Improve, [https://jarvislabs.ai/blog/vllm-optimization-techniques](https://jarvislabs.ai/blog/vllm-optimization-techniques)  
> 81. vLLM v1: Request Scheduling and Prefix Caching Broken Down, [https://medium.com/@satyamgoyal\_83363/vllm-v1-request-scheduling-and-prefix-caching-broken-down-ff298429fbc8](https://medium.com/@satyamgoyal_83363/vllm-v1-request-scheduling-and-prefix-caching-broken-down-ff298429fbc8)  
> 82. Significant speedup observed with long common prefix between v0, [https://discuss.vllm.ai/t/significant-speedup-observed-with-long-common-prefix-between-v0-11-0-and-v0-12-0/2379](https://discuss.vllm.ai/t/significant-speedup-observed-with-long-common-prefix-between-v0-11-0-and-v0-12-0/2379)  
> 83. \[Bug\]: Prefix-cache 0% hit on re-sent request — DeepSeek-V4-Flash, [https://github.com/vllm-project/vllm/issues/42948](https://github.com/vllm-project/vllm/issues/42948)  
> 84. \[Bug\] Severe GPU Memory Leak and OOM with Native KV ... - GitHub, [https://github.com/vllm-project/vllm/issues/44181](https://github.com/vllm-project/vllm/issues/44181)  
> 85. Real-Time AI Inference Systems: Speculative Decoding, KV Cache, [https://kumarshivam-66534.medium.com/real-time-ai-inference-systems-speculative-decoding-kv-cache-streaming-architecture-f8812f7e25dd](https://kumarshivam-66534.medium.com/real-time-ai-inference-systems-speculative-decoding-kv-cache-streaming-architecture-f8812f7e25dd)  
> 86. \[Bug\]\[vllm-omni\] Qwen3-TTS crashes under concurrent TTS ... - GitHub, [https://github.com/vllm-project/vllm/issues/44933](https://github.com/vllm-project/vllm/issues/44933)  
> 87. Can You Run AI Calling Models Locally? Privacy-First Speech-to, [https://www.autointerviewai.com/blog/can-you-run-ai-calling-locally-privacy-speech-models-2026](https://www.autointerviewai.com/blog/can-you-run-ai-calling-locally-privacy-speech-models-2026)  
> 88. KV Cache Memory: The Real Cost of Long-Context Inference, [https://intuitionlabs.ai/articles/kv-cache-memory-long-context-inference-cost](https://intuitionlabs.ai/articles/kv-cache-memory-long-context-inference-cost)  
> 89. A self-hosted, private voice assistant for a smart home - Reddit, [https://www.reddit.com/r/selfhosted/comments/1rj8bso/a\_selfhosted\_private\_voice\_assistant\_for\_a\_smart/](https://www.reddit.com/r/selfhosted/comments/1rj8bso/a_selfhosted_private_voice_assistant_for_a_smart/)  
> 90. Solving Voice AI Latency: From 5 Seconds to Sub-1 ... - Medium, [https://medium.com/@reveorai/solving-voice-ai-latency-from-5-seconds-to-sub-1-second-responses-d0065e520799](https://medium.com/@reveorai/solving-voice-ai-latency-from-5-seconds-to-sub-1-second-responses-d0065e520799)  
> 91. KV Caching with vLLM, LMCache, and Ceph, [https://ceph.io/en/news/blog/2025/vllm-kv-caching/](https://ceph.io/en/news/blog/2025/vllm-kv-caching/)  
> 92. vLLM Docs - GitHub Gist, [https://gist.github.com/rbiswasfc/678e4c78258480dcb6214efeedbe5af8](https://gist.github.com/rbiswasfc/678e4c78258480dcb6214efeedbe5af8)  
> 93. vLLM GPU Sizing Guide for LLM Inference - centron GmbH, [https://www.centron.de/tutorials/vllm-gpu-sizing-guide-for-llm-inference](https://www.centron.de/tutorials/vllm-gpu-sizing-guide-for-llm-inference)
