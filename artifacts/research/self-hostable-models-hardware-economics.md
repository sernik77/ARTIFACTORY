---
title: "Self-Hostable AI Models: Hardware and VRAM Economics"
kind: research
status: stable
summary: Self-hostable AI models (2025-2026) analysed by minimum hardware requirements and VRAM economics.
tags: [local-ai, self-hosting, vram, hardware]
created: 2026-03-25
updated: 2026-03-25
origin: "https://docs.google.com/document/d/1NnnPxzIcUwpJ6TUkfDjeSRnsSNQn9wsxIUtEZ-Ni44I"
---

# **AETHER RESEARCH ENGINE: EXHAUSTIVE ANALYSIS OF SELF-HOSTABLE AI MODELS (2025-2026)**

## **1. RAW HARDWARE REQUIREMENTS (LOWEST) AND VRAM ECONOMICS**

The physical deployment of open-weight artificial intelligence in 2026 is fundamentally governed by the strict mathematics of Video RAM (VRAM) allocation. The computational bottleneck for local inference has shifted away from raw Processing Unit (GPU/NPU) compute cycles and directly toward memory bandwidth and capacity.1 A model cannot execute efficiently if its weights, activations, and attention mechanisms exceed the localized hardware memory, forcing offloading to significantly slower system RAM via the PCIe bus, which degrades generation speeds to unusable thresholds.1

The VRAM calculation for self-hosting encompasses four primary variables: Base Model Weights, Activations, Framework Overhead, and the Key-Value (KV) Cache.4 The KV Cache represents the most volatile variable, as it scales linearly or quadratically with the context window size depending on the attention architecture.1

The evolution of context windows from the 2023 standard of 4K-8K tokens to the 2026 baseline of 128K-1M+ tokens necessitates architectural workarounds.6 Models utilizing Grouped-Query Attention (GQA) map multiple KV heads to a single query head, while Multi-Head Latent Attention (MLA) compresses the KV cache into a latent space, drastically reducing memory pressure.4

## **Baseline Minimum VRAM Requirements for Inference**

| Context Window | Baseline Memory Requirement (4-bit INT4 Quantization) | Primary Bottleneck | Suitable Hardware |
| :---- | :---- | :---- | :---- |
| **4K tokens** | ~2 GB to 4 GB | Base Weights | Mobile NPU, Integrated Graphics 6 |
| **8K tokens** | ~4 GB to 8 GB | Activations | RTX 3060, RTX 4060 6 |
| **32K tokens** | ~16 GB to 24 GB | KV Cache | RTX 4090, RX 7900 XTX 6 |
| **128K tokens** | ~64 GB to 96 GB | KV Cache & Framework Overhead | Mac Studio 128GB, Multi-GPU 6 |
| **1M+ tokens** | ~300 GB+ | Massive KV Cache | Distributed Cluster (8x H100) 6 |

* **Quantization Impact:** Storing a 7B to 9B parameter model in full 16-bit float (FP16) requires ~14-16 GB of VRAM. Using 8-bit (INT8) reduces this to ~7-9 GB, while 4-bit (INT4 / Q4\_K\_M) compresses it to ~4-6 GB.2  
* **KV Cache Mathematics:** For an 8B parameter model at an 8K context window, the attention mechanism alone requires 2-3 GB of VRAM.5 Scaling a model like GLM-4.7-Flash (17.05 GB base weights) to a 32K context requires 1.63 GB for the KV Cache (19.35 GB total). Pushing it to a 64K context requires 3.28 GB for the KV cache (21.00 GB total).9  
* **DeepSeek-R1 3B Allocation Profile:** When deployed on a 12GB RTX 3060 using FP16 precision and MLA, the model consumes 7.52 GB total (62.7% of available VRAM). The breakdown consists of 6.00 GB for base weights (79.9%), 1.01 GB for framework overhead (13.4%), 0.40 GB for activations (5.3%), and 0.10 GB for the KV cache at a 1,024 token sequence.4  
* **Power and Environmental Cost:** Running inference on an RTX 3060 draws approximately 128W, costing \$0.015 per hour. Sustained continuous operation results in 1.46 kg of CO2 emissions per day (534.6 kg annually).4  
* **System Memory Offloading:** A 14GB model can technically load onto a laptop with 16GB of system RAM, but CPU-only inference lacks parallel processing capabilities, resulting in severe performance degradation and OS instability.1

## **2. HARDWARE REQUIREMENTS TO CAPABILITY RATIO (BEST VALUE FOR EACH SIZE)**

The ratio of hardware cost to AI capability defines the self-hosting value proposition in 2026. Maximizing this ratio requires matching the parameter size and quantization level of a model to the exact VRAM limits of consumer or prosumer graphics cards, ensuring the entire model and its KV cache fit within the GPU without spilling over to system RAM.1

The 8GB VRAM tier is widely considered the minimum "sweet spot" for modern local inference. At this capacity, 7B to 9B parameter models encoded with 4-bit quantization fit comfortably, utilizing ~4 GB for parameters and leaving ~3 GB for the attention cache and system buffers.5

## **Top Hardware Configurations for Capability Value**

| Hardware Profile | Model Params (Max) | Model Size (Max) | Memory Usage Scale | Tool Use | Best for | Major Pros | Major Cons |
| :---- | :---- | :---- | :---- | :---- | :---- | :---- | :---- |
| **Intel Arc B580 / RTX 4060 Ti (16GB)** | Up to 14B | ~9 GB (INT4) | 16GB limits context to 32K at 14B. | Yes | Budget value; entry-level coding and local chatbots 10 | Best price-to-performance ratio (\$500) 11 | Cannot run complex 30B+ reasoning models. |
| **AMD RX 7900 XTX (24GB)** | Up to 35B | ~19 GB (INT4) | 24GB allows 32B models with 8K context. | Yes | Budget high-VRAM processing within AMD ecosystem 12 | 960 GB/s bandwidth; massive 24GB capacity for \$900 12 | ROCm stack remains immature on Windows vs CUDA.13 |
| **Nvidia RTX 4090 (24GB)** | Up to 35B | ~19 GB (INT4) | 24GB allows 32B models with 8K context or 70B via heavy IQ quant. | Yes | Advanced 30B reasoning, serious development, multimodal VLMs 12 | 1.01 TB/s bandwidth; dominant CUDA support 12 | \$2,000 price point; rapid depreciation.12 |
| **Nvidia RTX 5090 (32GB)** | Up to 70B (MoE) | ~25 GB (INT4) | 32GB allows unquantized 14B or INT4 70B sparse models. | Yes | Top-tier consumer enthusiast; multi-agent deployments 11 | 1,792 GB/s bandwidth; superior throughput 8 | Artificial market distortion pushes price to \$3,000+.8 |
| **Mac Studio (M3/M4 Ultra 96GB/128GB)** | Up to 120B | ~70 GB (INT4) | Unified memory enables massive context and large dense models. | Yes | Enterprise inference requiring data privacy 8 | Eliminates NVLink complexity; fits 70B-120B cleanly 8 | Slower tokens-per-second than discrete Nvidia GPUs.8 |

* **Decentralized Rental vs. Ownership ROI:** Purchasing an RTX 4090 (\$1,599-\$2,500) matches the cumulative cost of renting an A100 GPU after approximately 3,500 hours of active inference.12  
* **Cloud Pricing:** Decentralized networks like Fluence and Vast.ai offer RTX 4090 instances for \$0.44 to \$0.64 per hour. A100 80GB instances rent for \$0.60 to \$4.15 per hour, representing a 50-80% discount compared to centralized providers like AWS (\$5-\$7/hour).12  
* **Memory Bandwidth Bottleneck:** The RTX 5090's 32GB GDDR7 VRAM boasts 1,792 GB/s bandwidth, fundamentally outpacing the RTX 4090's 1,008 GB/s GDDR6X bandwidth, which translates directly to higher token generation speeds during batch processing.8  
* **Scaling Complexity:** Multi-GPU configurations (2-8 GPUs connected via NVLink) scale linearly up to 640GB total VRAM. Beyond this threshold, multi-node deployments introduce severe communication latency overhead.8

## **3. INFERENCE SPEED AND THROUGHPUT**

The speed of an AI model is bifurcated into two distinct metrics: Time to First Token (TTFT) and Generation Speed (Output Throughput measured in tokens per second, or tok/s). TTFT is critical for interactive applications and voice agents, representing the latency between user input and the model's initial response. Generation speed governs the rapid synthesis of large documents, codebases, and synthetic data.4

Throughput heavily depends on the precision format (FP16 vs INT4), memory bandwidth, and the batch size. Consumer GPUs like the RTX 4090 and 5090 excel at single-user inference (batch size 1), while data center GPUs (H100, B200) are engineered to maintain high tokens-per-second across massive concurrent request queues.2

## **Top Models by Speed and Hardware Benchmarks**

| Parameter | Kimi K2.5 | DeepSeek V3.2 | Qwen2.5-Coder-7B | DeepSeek-R1:32B |
| :---- | :---- | :---- | :---- | :---- |
| **Model Params** | ~1T total 19 | 685B total 20 | 7B | 32B |
| **Model Size** | Cloud/Cluster | Cluster | ~4.5 GB (INT4) | ~19 GB (INT4) |
| **Memory Usage Scale** | Distributed API | Distributed API | Easily fits 8GB VRAM | Fits 24GB VRAM |
| **Tool Use** | Yes | Yes | Yes | Yes |
| **Best For** | Ultra-fast data synthesis | Agentic batch processing | Real-time coding assistance | Local complex problem solving |
| **Major Pros** | Fastest frontier inference: **334 tok/s** 18 | Highly permissive MIT license 21 | Hits **5,841 tok/s** on RTX 5090 (Batched) 13 | Distilled reasoning on consumer hardware |
| **Major Cons** | Commercial deployment limits 22 | Slow TTFT (1.18s) compared to Kimi 18 | Low context ceiling on 8GB cards | Slow generation due to CoT overhead |

* **RTX 5090 vs RTX 4090 Benchmarks:** For high-throughput API production servers, the RTX 5090 processes Qwen3-4B at 954 tok/s, outperforming the RTX 4090 at 706 tok/s (a 26% difference).16 For single-user inference on deepseek-r1:32b, the RTX 5090 generates 71 tok/s compared to the 4090's 45 tok/s (a 37% difference).16  
* **High-Throughput Anomaly:** The RTX 5090 achieves an astounding 5,841 tokens/sec on Qwen2.5-Coder-7B in highly batched scenarios, operating 2.6x faster than a data center A100.13  
* **Frontier Model Latency:** Kimi K2.5 boasts an exceptional TTFT of 0.31s, responding 8x faster than Claude Opus 4.6 (2.48s). Kimi also generates at 334 tok/s, operating 4x faster than GPT-5.4 (~78 tok/s) and 7x faster than Opus (46 tok/s).18  
* **Reasoning Overhead:** DeepSeek R1 inherently operates at slower generation speeds (~30 tok/s) because it must process thousands of hidden Chain-of-Thought (CoT) reasoning tokens before outputting the final answer.18

## **4. REASONING MODELS**

In 2026, raw computational reasoning has eclipsed standard conversational imitation. The paradigm shifted with the introduction of Reinforcement Learning (RL) powered reasoning models that utilize intrinsic Chain-of-Thought (CoT) loops. Instead of predicting the next most likely word based on semantic patterns, these models are trained to think iteratively, verify their own mathematical logic, test hypotheses, and backtrack before delivering a final response. This architectural design significantly mitigates hallucinations and excels in PhD-level science, multi-step logic, and competitive programming.22

The standard architecture for reasoning models relies heavily on Sparse Mixture-of-Experts (SMoE). SMoE permits a model to possess hundreds of billions of parameters to store vast knowledge, but it only activates a tiny fraction (the "experts") for any given token, enabling massive intelligence to run efficiently on limited computational budgets.19

## **Top Reasoning Models**

| Parameter | DeepSeek Reasoner (R1) | Qwen 3 Next Thinking | Llama 4 Maverick | GPT-OSS 120B | GLM-4.7 Flash |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **Model Params** | **671B** total 22 | **80B** total 22 | **400B** total 22 | **117B** total 19 | **30B** total 23 |
| **Model Size** | ~350 GB (INT4) | ~45 GB (INT4) | ~220 GB (INT4) | ~65 GB (INT4) | ~17 GB (INT4) |
| **Memory Usage Scale** | Multi-node (H100/B200 x8) 8 | Multi-GPU (2x 24GB) or Mac Studio | Multi-node cluster | Multi-GPU (3x 24GB) | Single 24GB GPU |
| **Tool Use** | Yes (Code/Math focus) 22 | Yes (Agentic pipelines) 22 | Yes (Native multimodal) 22 | Yes (CoT APIs) 22 | Yes |
| **Best For** | Academic research, PhD math 22 | Efficient hybrid reasoning 22 | Large-scale multi-model logic 22 | Enterprise synthetic data generation 22 | Lightweight high-speed logic 23 |
| **Major Pros** | Hits **97.3%** Math-500 score; zero hallucination logic 22 | **3B** active params; dual think/fast mode; AIME25 **87.8%** 22 | Fast for its size; costs just **\$0.19/1M** tokens 22 | Hits **97.9%** AIME 2025; MIT licensed 22 | Extremely fast; fits entirely on consumer hardware |
| **Major Cons** | Massive token consumption; slow output 22 | Shallow depth on multi-disciplinary queries 22 | Demands H100 DGX host 22 | Highly verbose; long unnecessary responses 22 | Known to hallucinate on non-technical prompts 24 |

* **Parameter Efficiency:** DeepSeek R1 possesses 671B total parameters but only activates 37B per token.22 Qwen 3 Next Thinking (80B) activates merely 3B parameters per token, representing the apex of compute efficiency.22  
* **Benchmark Dominance:** On the graduate-level science reasoning benchmark (GPQA Diamond), Qwen 3.5 scores 88.4%, Kimi K2.5 scores 87.6%, and GLM-5 scores 86.0%.21  
* **Math Superiority:** GPT-OSS 120B holds the record for mathematical deduction, scoring an unprecedented 97.9% on AIME 2025.22  
* **Distillation:** For consumer hardware, DeepSeek has distilled the R1 reasoning patterns into smaller dense models, notably the DeepSeek-R1-Distill-Qwen-32B, which provides advanced CoT logic within a 24GB VRAM footprint.26

## **5. CODING**

Coding capability requires high structural stability, flawless syntax generation, and the ability to maintain context across vast repositories. The shift in 2026 moves from localized autocomplete to full agentic software engineering—where the LLM ingests GitHub issues, reads the codebase, writes the patch, and executes unit tests autonomously.27

Open-weight coding models have saturated the benchmark limits, proving that expensive proprietary APIs are no longer strictly necessary for professional development.

## **Top Coding Models**

| Parameter | Qwen3 Coder 480B | Qwen3 30B-A3B Coder | DeepSeek Coder V2 | Llama 3.3 70B Instruct |
| :---- | :---- | :---- | :---- | :---- |
| **Model Params** | **480B** total 28 | **30B** total | **236B** total 29 | **70B** total 30 |
| **Model Size** | ~260 GB (INT4) | ~17 GB (INT4) | ~130 GB (INT4) | ~38 GB (INT4) |
| **Memory Usage Scale** | Multi-node cluster | Single 24GB GPU | Multi-GPU (6x 24GB) | Multi-GPU (2x 24GB) |
| **Tool Use** | Yes | Yes | Yes | Yes |
| **Best For** | Massive repository refactoring 28 | Everyday developer copilot | Complex backend C++ / Python algorithms 30 | All-around coding assistance 31 |
| **Major Pros** | **262K** context; optimized for agentic tool use 28 | Hits **91.0** ArenaHard; fast local iteration 32 | **21B** active params; high inference efficiency 29 | Highly reliable; strong community ecosystem 30 |
| **Major Cons** | Impractical to self-host natively 28 | Lacks depth of the 480B variant | Superseded by newer R1 logic loops | Lacks specialized coding distillation |

* **Real-World Resolution Rates:** The SWE-bench Verified benchmark tests a model's ability to solve real-world GitHub issues. The proprietary Claude Opus 4.6 leads with 80.8%, but open-weight Kimi K2.5 follows closely at 76.8%, and DeepSeek V3.2 achieves 73.0%.18  
* **The Qwen3 Dominance:** Qwen3 Coder models consistently outperform competitors in syntax structure. The 480B variant utilizes 8 out of 160 experts per forward pass (35B active params) to maintain a massive 262K token context window.28 The smaller Qwen3-30B-A3B variant achieves a CodeForces Elo of 1974 and an ArenaHard score of 91.0, beating GPT-4o (85.3).32  
* **Vibe Coding and Syntax Adherence:** In blind A/B tests against DeepSeek V3 and Mistral, the Qwen2.5-72B model is continuously rated as the superior model for modern stack integration (Rails, Tailwind, Vue), avoiding the "glue" code hallucinations common in smaller 7B models.33

## **6. TOOL USE AND AGENTIC ORCHESTRATION**

Tool use separates passive text generators from active digital agents. An LLM's ability to natively output structured JSON, call external APIs, parse web search results, execute mathematical calculators, and manipulate file systems determines its viability for enterprise automation.34

Models lacking native tool-use training fail at agentic orchestration because they cannot reliably format the specific syntax required to trigger external functions, often wrapping code blocks in conversational filler that breaks the parsing scripts.

## **Top Tool Use Models**

| Parameter | Command R+ | GLM-5 | MiniMax M2.5 | Qwen 2.5 / 3.5 Series |
| :---- | :---- | :---- | :---- | :---- |
| **Model Params** | **104B** total (16B active) 30 | **744B** total 25 | **230B** total 25 | Varies (0.5B to 397B) 25 |
| **Model Size** | ~58 GB (INT4) | Cluster | ~125 GB (INT4) | Varies |
| **Memory Usage Scale** | Multi-GPU (3x 24GB) or Mac Studio | Multi-node | Multi-node | Varies heavily |
| **Tool Use** | Yes (Native Enterprise RAG) 34 | Yes (Reasoning integration) | Yes (Agentic optimized) 22 | Yes (Native JSON forcing) 34 |
| **Best For** | Complex RAG workflows; financial data 34 | Combining reasoning with API actions 23 | Office automation; multi-step tool logic 22 | High-reliability JSON output 34 |
| **Major Pros** | Multilingual (10+ languages); handles API errors natively 34 | Highest Chatbot Arena Elo (1460) 25 | Extremely fast API execution 36 | Excellent instruction following 34 |
| **Major Cons** | CC-BY-NC 4.0 license blocks commercial usage 30 | Massive hardware requirements | Lacks deep mathematical foundational logic | Smaller variants fail multi-step logic |

* **Native Architecture:** Command R+ is explicitly designed to recognize when to call a function, how to parse the returned results, and how to execute error handling natively, making it superior for complex Retrieval-Augmented Generation (RAG) pipelines.34  
* **Agentic Elo Ratings:** In the Chatbot Arena, GLM-5 ranks \#1 globally with an Elo of 1460, excelling at reasoning tasks intertwined with agentic tool calls.25  
* **JSON and Instruction Formatting:** The Qwen2.5 and 3.5 series underwent specific training to guarantee enhanced JSON output generation, an absolute necessity for integrating LLMs into programmatic backend services without parsing errors.34

## **7. GENERAL CAPABILITY**

General capability models prioritize factual knowledge retrieval, conversational fluidity, and multi-disciplinary competence over raw logic or coding. These are the foundational models utilized for customer service chatbots, encyclopedic queries, summarization, and creative drafting.

The primary evaluation metric for general intelligence is the MMLU-Pro (Massive Multitask Language Understanding) benchmark, which tests advanced knowledge across a 10-option format to eliminate random guessing, and the LMArena Elo, which measures subjective human preference.18

## **Top General Capability Models**

| Parameter | Mistral Large 3 | Llama 4 Scout | Kimi K2.5 | Devstral-2-123B |
| :---- | :---- | :---- | :---- | :---- |
| **Model Params** | **675B** total 25 | **109B** total 25 | ~1T total 25 | **123B** total 25 |
| **Model Size** | ~370 GB (INT4) | ~60 GB (INT4) | Cluster | ~68 GB (INT4) |
| **Memory Usage Scale** | Multi-node | Multi-GPU or Mac Studio | Distributed API | Multi-GPU |
| **Tool Use** | Yes | Yes | Yes | Yes |
| **Best For** | General multilingual text synthesis 30 | Full-history long context reasoning 20 | Broad enterprise knowledge worker 36 | Complex software and general assistance |
| **Major Pros** | Permissive Apache 2.0 license; multimodal 20 | Record-breaking **10M token** context window 20 | Achieves **87.1%** MMLU-Pro; fast latency 18 | Highly capable generalist replacement 24 |
| **Major Cons** | Computationally massive 25 | Impossible to self-host locally at full context | Restricted distribution 18 | High VRAM requirements for small teams |

* **Knowledge Saturation:** The benchmark for global knowledge (MMLU-Pro) is nearing saturation. GPT-5.4 leads with 88.5%, but open models follow tightly: Kimi K2.5 (87.1%), DeepSeek V3.2 (85.0%), and DeepSeek R1 (84.0%) all surpass the proprietary Claude Opus 4.6 (82.0%).18  
* **Context Expansion:** Llama 4 Scout introduces an unprecedented 10-million token context window, allowing the model to ingest multi-year project histories, entire libraries of documentation, and full codebases simultaneously.20  
* **Local Deployment Dominance:** For standard 24GB VRAM local deployments seeking general intelligence without heavy agentic routing, users frequently default to Devstral-2 (quantized) or the Qwen3 series (30B), which provide the most human-like conversational nuance and structural formatting.24

## **8. MODEL SIZE (SMALLEST MODELS FOR EDGE AI)**

The demand for Small Language Models (SLMs)—defined as architectures under 10 billion parameters—has exploded to satisfy the requirements of Edge AI, mobile processing, IoT devices, and strict data-privacy environments.37 By executing directly on the user's hardware (e.g., a smartphone NPU or a laptop CPU), SLMs eliminate cloud subscription fees, guarantee zero latency from network round-trips, and ensure sensitive data never traverses the internet.37

## **Top Small Language Models (Edge AI)**

| Parameter | Gemma 3 (270M) | DeepSeek R1 (1.5B) | SmolLM2 (1.7B) | Ministral 3 (3B) | Phi-4-mini (3.8B) |
| :---- | :---- | :---- | :---- | :---- | :---- |
| **Model Params** | **0.27B** 37 | **1.5B** 39 | **1.7B** 39 | **3.4B** (Language) + 0.4B (Vision) 40 | **3.8B** 37 |
| **Model Size** | **\< 1 GB** | **~1.1 GB** 39 | **~1 GB** 39 | **~2.2 GB** (INT4) | **~2.5 GB** (INT4) |
| **Memory Usage Scale** | Negligible; fits in smartphone RAM | ~16 GB RAM for CPU inference 39 | Mobile device / SBC RAM | Fits under 8GB VRAM 40 | Fits cleanly on 8GB VRAM cards |
| **Tool Use** | No | Yes (Math focus) | No | Yes (Basic) | Yes |
| **Best For** | Mobile battery-efficient tasks 38 | Offline math and logic verification 39 | Embedded IoT operations 39 | Edge deployment requiring vision 40 | Laptop-based advanced reasoning 37 |
| **Major Pros** | Uses **\<1%** battery for 25 chats (Pixel 9) 38 | Scored **28.9%** on AIME 2024 (beats GPT-4o) 39 | Trained on massive 11 Trillion tokens 39 | Native multimodal; optimized for edge 40 | Massive **128K** context; MMLU **67.3%** 37 |
| **Major Cons** | Severe lack of world knowledge | Fails conversational and creative tasks 39 | Lacks depth in multi-step reasoning | Limited context window | Slower throughput than smaller peers |

* **Battery Efficiency:** Google's Gemma 3 270M model represents the pinnacle of mobile optimization. Using Quantization-Aware Training (QAT) checkpoints directly from the factory, it operates natively on INT4 precision, consuming less than 1% of a Pixel 9 Pro's battery during 25 full conversation turns.37  
* **Distilled Intelligence:** DeepSeek distilled the reasoning loops of its massive R1 model down into a 1.5B parameter variant (based on Qwen 2.5). Despite its tiny footprint, it excels in mathematical environments, scoring 28.9% on AIME 2024—outperforming models 100x its size.39  
* **The 4B Benchmark Leader:** Microsoft's Phi-4-mini proves that extreme data curation beats raw parameter scaling. At 3.8B parameters, it supports a 128K context window, 23 languages, and achieves a 67.3% MMLU score, outclassing Meta's Llama 3.2 3B (61.8%) across all metrics.37

## **9. UNCENSORED CAPABILITIES (TEXT, IMAGE, VIDEO)**

Standard AI models undergo intensive RLHF (Reinforcement Learning from Human Feedback) alignment to refuse controversial, explicit, or dangerous queries. However, this "safety tax" heavily degrades the model's capacity for raw logical deduction, unbiased code generation, and creative fiction.42 Uncensored models remove these guardrails entirely.

The methodology has evolved from simple prompt engineering to "abliteration" (orthogonal projection techniques) that mathematically erase the refusal vector from the model's weights, ensuring it cannot lecture the user or deny a prompt.42

## **Top Uncensored Text Models**

| Parameter | LLaMA-3.2 Dark Champion | Dolphin 3.0 | Nous Hermes 3 |
| :---- | :---- | :---- | :---- |
| **Model Params** | **18.4B** (MoE) 42 | **8B** to **24B** 42 | **8B** to **70B** 42 |
| **Model Size** | ~12 GB (INT4) | ~6 GB to ~14 GB (INT4) | Varies by variant |
| **Memory Usage Scale** | High (Due to 128K context limits) | Low to Moderate | Scales with context needs |
| **Tool Use** | Yes | Yes (Steerable control) | Yes |
| **Best For** | High-demand unstructured document analysis 42 | Unfiltered coding, math, and logic 42 | Deep role-playing and storytelling 42 |
| **Major Pros** | Massive **128K** context; completely unaligned 42 | "God-tier" instruction following 42 | Best-in-class character adherence 42 |
| **Major Cons** | Heavy VRAM pressure to utilize full context | Direct, sterile output; poor at creative prose 42 | Chatty; fails strict direct-answer logic 42 |

* **Cognitive Engines:** LLaMA-3.2 Dark Champion Abliterated utilizes an 8x3B MoE design, acting as an unrestricted cognitive engine capable of analyzing explicit biological data, political theory, or restricted network codebases without triggering alignment refusals.42  
* **The Logician:** Dolphin 3.0 strips away conversational personality entirely to focus on pure mathematical precision and steerability, making it the premier choice for developers who require raw, unfiltered codebase generation.42

## **Top Uncensored Image and Video Models**

The uncensored visual generation space is decentralized, running locally via ComfyUI or accessed via privacy-first platforms.

| Parameter | Z-Image-Turbo | Flux.2 (Uncensored) | Wan 2.6 (Video) | SDXL Checkpoints (Lustify / Big Love) |
| :---- | :---- | :---- | :---- | :---- |
| **Model Params** | N/A (Latent Diffusion) | **12B** equivalent | **14B** equivalent 44 | **3.5B** equivalent |
| **Model Size** | ~6 GB | ~24 GB (FP16) | ~30 GB | ~6.5 GB |
| **Memory Usage Scale** | Low (Runs on 8GB VRAM) 45 | High (Requires 16GB-24GB VRAM) | Extreme (Requires 24GB+ VRAM) | Moderate (12GB VRAM optimal) |
| **Tool Use** | Integrated with ComfyUI | Integrated with ComfyUI | ComfyUI / Nodes | Integrated with A1111/ComfyUI |
| **Best For** | Consistent cinematic scenes 46 | Photorealistic, text-heavy image gen | High fidelity I2V motion | High-fidelity anatomical rendering 46 |
| **Major Pros** | Fast generation; high community rating 46 | Flawless prompt adherence; excellent anatomy | Smooth physics and transitions 44 | Massive Civitai community support 47 |
| **Major Cons** | Struggles with complex hand anatomy | Extremely resource intensive | Excruciatingly slow local generation times | Requires complex prompt engineering |

* **Platform Ecosystems:** Civitai remains the dominant hub, hosting over 14,092 NSFW-tagged LoRAs and checkpoints.48 Platforms like PixelBunny.ai and Venice AI provide uncensored generations natively for users lacking the 24GB VRAM required to run heavy models like Flux.2 and Wan 2.6 locally.44

## **10. MULTIMODAL, UNIQUE, AND SPECIAL ABILITIES**

The integration of multiple modalities—vision, video generation, and audio synthesis—into foundational models represents the most resource-intensive frontier of self-hosted AI.

## **Top Vision Language Models (VLMs)**

| Parameter | InternVL3-78B | Qwen2.5-VL-72B | LLaMA 3.2-Vision | DeepSeek-VL2 |
| :---- | :---- | :---- | :---- | :---- |
| **Model Params** | **78B** 50 | **72B** 50 | Multiple variants 50 | MoE architecture 50 |
| **Memory Usage Scale** | High (Multi-GPU) | High (Dual 24GB GPUs) | Moderate to High | Highly efficient 50 |
| **Tool Use** | Yes | Yes (Video understanding) | Yes (OCR) | Yes |
| **Best For** | 3D Modeling/Engineering analysis 50 | Multilingual UI automation 50 | Heavy OCR document parsing 50 | Portable offline diagnostics 50 |
| **Major Pros** | Scored **72.2%** on MMMU 50 | Flawless video localization 50 | Highly accurate text extraction | Lean compute requirements 50 |
| **Major Cons** | Extremely VRAM heavy | Overkill for simple QA tasks | Lacks deep spatial awareness | Lower benchmark scores than 70B models |

* **Progressive Scaling:** InternVL2.5 utilizes a staged training approach. It aligns its vision encoder (InternViT) with a smaller 20B LLM first, optimizing fundamental cross-modal alignment without massive compute costs. The weights are then transferred to a 72B LLM, drastically reducing the data and compute required to reach state-of-the-art accuracy.51

## **Top Video Generation Models**

| Parameter | LTX-2 | Wan 2.2 | HunyuanVideo 1.5 | Mochi 1 |
| :---- | :---- | :---- | :---- | :---- |
| **Model Params** | Optimized variants 52 | **14B** and **5B** variants 53 | **13B+** 53 | **10B** 53 |
| **Memory Usage Scale** | Ultra-efficient (**12GB** VRAM) 52 | High (**24GB** VRAM) | Extreme (Multi-GPU) | High (**24GB** VRAM) |
| **Best For** | Fast consumer hardware generation | Cinematic 720p/24fps video 54 | High-fidelity structural video 55 | Fluid physics and motion 54 |
| **Major Pros** | **18x** more efficient; synced audio 52 | Best quality-to-efficiency balance 56 | Superior motion clarity and stability 55 | Apache 2.0 license; hackable 54 |
| **Major Cons** | Lower raw fidelity than 14B models | Color over-saturation issues 56 | Excruciatingly slow inference 56 | Lower maximum resolution limits 56 |

* **Consumer Viability:** Video synthesis is notoriously VRAM intensive, requiring meticulous batching just to output seconds of footage.53 LTX-2 broke this barrier, generating high-resolution, 32-second clips with synced audio on a single 12GB GPU, operating 18x more efficiently than competing architectures.52

## **Top Audio and Speech Models**

| Parameter | Fish Speech V1.5 | CosyVoice2-0.5B | IndexTTS-2 |
| :---- | :---- | :---- | :---- |
| **Model Params** | Scale optimized 57 | **0.5B** 57 | Scale optimized |
| **Memory Usage Scale** | Low (**8GB** VRAM) | Ultra-Low (Edge deployment) | Low |
| **Best For** | Multilingual voice cloning 58 | Real-time conversation/streaming 57 | Emotional dialogue generation 57 |
| **Major Pros** | Industry-leading accuracy; expressive control 57 | Ultra-low latency (**150ms**) 57 | Zero-shot emotion/duration control 57 |
| **Major Cons** | Requires quality reference audio | Lower fidelity on musical composition | Less suited for ultra-long form reading |

* **Speech-to-Text (STT) Benchmarks:** For local transcription, Whisper Large V3 maintains dominance with a 7.4% Word Error Rate (WER) requiring ~10GB VRAM. Distil-Whisper Large V3 provides near-identical accuracy while dropping the VRAM requirement to ~5GB.59

## **Specialized Sector Domains**

* **Law and Finance:** Falcon 180B excels here. Built for fast inference at scale, it eschews creative storytelling in favor of rigid structural compliance, making it superior for analyzing high-stakes financial reports and legal contracts.60  
* **Medicine:** General models frequently hallucinate clinical diagnostics. Specialized models like GLM-4-9B-Chat and Med-PaLM 2 are explicitly fine-tuned on medical corpora to deliver high accuracy while maintaining strict HIPAA compliance via localized, offline execution.61

## **11. EASY TO MODIFY OR TRAIN (FINE-TUNING)**

The open-source ecosystem thrives on fine-tuning. Base models understand language patterns; fine-tuned models execute specific enterprise instructions.35 Modifying these models securely offline allows corporations to inject proprietary data, adapt visual encoders, and customize coding syntax without exposing secrets to cloud endpoints.

Fine-tuning in 2026 relies heavily on Low-Rank Adaptation (LoRA) and Quantized LoRA (QLoRA). Instead of updating all 70 billion parameters of a model, LoRA freezes the base weights and introduces small trainable adapter matrices. For a 7B model, these adapters represent just 0.0024% of the total parameters, reducing the VRAM required for training from hundreds of gigabytes down to consumer-tier hardware limits.12

## **Top Fine-Tuning Frameworks**

| Parameter | Axolotl | Unsloth | Torchtune |
| :---- | :---- | :---- | :---- |
| **Format Style** | Configuration-based (YAML) 62 | Notebook/Code-based (Python) 62 | PyTorch Native 63 |
| **Hardware Scale** | Multi-GPU / Cluster 63 | Single GPU optimization (RTX 4090) 64 | Single to Multi-GPU |
| **Best For** | Production pipelines; distributed networks 63 | Rapid, low-VRAM iteration on consumer GPUs 63 | Deep researchers needing raw PyTorch 63 |
| **Major Pros** | Native multimodal support (LLaMA-Vision) 64 | **2x faster** training; GRPO support for MoE 64 | Maximum granular control |
| **Major Cons** | Steep learning curve for YAML configs | Multi-GPU support limited on free tier 62 | Lacks high-level abstraction conveniences |

* **The GRPO Shift:** The release of DeepSeek R1 proved that advanced reasoning capabilities stem from Group Relative Policy Optimization (GRPO) training rather than pure scaling.64 Unsloth rapidly integrated GRPO, allowing developers to train reasoning capabilities into local models using a single 24GB RTX 4090 within weeks of the methodology's release.64  
* **Single-GPU MoE Training:** Historically, training Mixture-of-Experts architectures required server racks. In 2026, frameworks like Unsloth enable the fine-tuning of the Qwen3 30B A3B (MoE variant) using just 17.5GB of VRAM, drastically lowering the barrier to entry for highly capable, sparse model creation.64  
* **Production Reproducibility:** Axolotl dominates enterprise environments because its YAML configuration approach guarantees exact reproducibility across training runs, mitigating the errors introduced by maintaining bespoke Python tuning scripts.62 It also handles complex sample packing algorithms automatically to maximize GPU utilization during dataset ingestion.63

#### **Works cited**

> 1. Context Kills VRAM: How to Run LLMs on consumer GPUs | by Lyx | Medium, accessed on March 25, 2026, [https://medium.com/@lyx\_62906/context-kills-vram-how-to-run-llms-on-consumer-gpus-a785e8035632](https://medium.com/@lyx_62906/context-kills-vram-how-to-run-llms-on-consumer-gpus-a785e8035632)  
> 2. The right GPU for LLM inference in 2026 | Hivenet, accessed on March 25, 2026, [https://compute.hivenet.com/post/best-gpu-for-llm-inference](https://compute.hivenet.com/post/best-gpu-for-llm-inference)  
> 3. Run AI Locally: The Best LLMs for 8GB, 16GB, 32GB Memory and Beyond - Micro Center, accessed on March 25, 2026, [https://www.microcenter.com/site/mc-news/article/best-local-llms-8gb-16gb-32gb-memory-guide.aspx](https://www.microcenter.com/site/mc-news/article/best-local-llms-8gb-16gb-32gb-memory-guide.aspx)  
> 4. Can You Run This LLM? VRAM Calculator (Nvidia GPU and Apple Silicon), accessed on March 25, 2026, [https://apxml.com/tools/vram-calculator](https://apxml.com/tools/vram-calculator)  
> 5. Best Local LLMs for 8GB VRAM: Complete 2025 Performance Guide, accessed on March 25, 2026, [https://localllm.in/blog/best-local-llms-8gb-vram-2025](https://localllm.in/blog/best-local-llms-8gb-vram-2025)  
> 6. Context Length Guide 2025: Master AI Context Windows for Optimal Performance & Results, accessed on March 25, 2026, [https://local-ai-zone.github.io/guides/context-length-optimization-ultimate-guide-2025.html](https://local-ai-zone.github.io/guides/context-length-optimization-ultimate-guide-2025.html)  
> 7. Agent Memory Below the Prompt: Persistent Q4 KV Cache for Multi-Agent LLM Inference on Edge Devices - arXiv.org, accessed on March 25, 2026, [https://arxiv.org/html/2603.04428v1](https://arxiv.org/html/2603.04428v1)  
> 8. Local LLM Hardware Guide 2025: GPU Specs & Pricing | Introl Blog, accessed on March 25, 2026, [https://introl.com/blog/local-llm-hardware-pricing-guide-2025](https://introl.com/blog/local-llm-hardware-pricing-guide-2025)  
> 9. llama.cpp VRAM Requirements: Complete 2026 Guide to GPU Memory for Local LLMs, accessed on March 25, 2026, [https://localllm.in/blog/llamacpp-vram-requirements-for-local-llms](https://localllm.in/blog/llamacpp-vram-requirements-for-local-llms)  
> 10. Local LLM Hardware Guide: Best Budget Options in 2025 | sanj.dev, accessed on March 25, 2026, [https://sanj.dev/post/affordable-ai-hardware-local-llms](https://sanj.dev/post/affordable-ai-hardware-local-llms)  
> 11. The Best GPUs for Local LLM Inference in 2025, accessed on March 25, 2026, [https://localllm.in/blog/best-gpus-llm-inference-2025](https://localllm.in/blog/best-gpus-llm-inference-2025)  
> 12. Best Budget GPU for AI in 2026: What Delivers the Lowest Cost per ..., accessed on March 25, 2026, [https://www.fluence.network/blog/best-budget-gpus/](https://www.fluence.network/blog/best-budget-gpus/)  
> 13. Benchmarked the main GPU options for local LLM inference in 2026 : r/LocalLLaMA - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1rk5ftz/benchmarked\_the\_main\_gpu\_options\_for\_local\_llm/](https://www.reddit.com/r/LocalLLaMA/comments/1rk5ftz/benchmarked_the_main_gpu_options_for_local_llm/)  
> 14. RTX 5090 vs RTX 4090 - GPU Benchmarks - Runpod, accessed on March 25, 2026, [https://www.runpod.io/gpu-compare/rtx-5090-vs-rtx-4090](https://www.runpod.io/gpu-compare/rtx-5090-vs-rtx-4090)  
> 15. The Complete Guide to Running LLMs Locally: Hardware, Software, and Performance Essentials - IKANGAI, accessed on March 25, 2026, [https://www.ikangai.com/the-complete-guide-to-running-llms-locally-hardware-software-and-performance-essentials/](https://www.ikangai.com/the-complete-guide-to-running-llms-locally-hardware-software-and-performance-essentials/)  
> 16. RTX 4090 vs RTX 5090 - GPU Benchmark Comparison | Trooper.AI, accessed on March 25, 2026, [https://www.trooper.ai/benchmarks-compare-RTX-4090-with-RTX-5090](https://www.trooper.ai/benchmarks-compare-RTX-4090-with-RTX-5090)  
> 17. Figuring out the best hardware : r/LocalLLM - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/LocalLLM/comments/1m4pb7v/figuring\_out\_the\_best\_hardware/](https://www.reddit.com/r/LocalLLM/comments/1m4pb7v/figuring_out_the_best_hardware/)  
> 18. Open-source models are production-ready — here's the data (5 models × 5 benchmarks vs Claude Opus 4.6 and GPT-5.4) : r/LocalLLaMA - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1ry4r56/opensource\_models\_are\_productionready\_heres\_the/](https://www.reddit.com/r/LocalLLaMA/comments/1ry4r56/opensource_models_are_productionready_heres_the/)  
> 19. Top 10 Open-source Reasoning Models in 2026 - Clarifai, accessed on March 25, 2026, [https://www.clarifai.com/blog/top-10-open-source-reasoning-models-in-2026](https://www.clarifai.com/blog/top-10-open-source-reasoning-models-in-2026)  
> 20. Most powerful LLMs (Large Language Models) in 2026 - Codingscape, accessed on March 25, 2026, [https://codingscape.com/blog/most-powerful-llms-large-language-models](https://codingscape.com/blog/most-powerful-llms-large-language-models)  
> 21. Open Source LLM Leaderboard 2026: Rankings, Benchmarks & the Best Models Right Now - VERTU® Official Site, accessed on March 25, 2026, [https://vertu.com/lifestyle/open-source-llm-leaderboard-2026-rankings-benchmarks-the-best-models-right-now/](https://vertu.com/lifestyle/open-source-llm-leaderboard-2026-rankings-benchmarks-the-best-models-right-now/)  
> 22. Best Open Source Reasoning Models in 2026: Top Picks for ..., accessed on March 25, 2026, [https://okara.ai/blog/best-open-source-reasoning-models](https://okara.ai/blog/best-open-source-reasoning-models)  
> 23. The Best Open-Source LLMs in 2026 - BentoML, accessed on March 25, 2026, [https://www.bentoml.com/blog/navigating-the-world-of-open-source-large-language-models](https://www.bentoml.com/blog/navigating-the-world-of-open-source-large-language-models)  
> 24. What is the best general-purpose model to run locally on 24GB of VRAM in 2026? - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1qlwibf/what\_is\_the\_best\_generalpurpose\_model\_to\_run/](https://www.reddit.com/r/LocalLLaMA/comments/1qlwibf/what_is_the_best_generalpurpose_model_to_run/)  
> 25. Best Self-Hosted LLM Leaderboard 2026 | Open-Weight Model Rankings for Enterprise, accessed on March 25, 2026, [https://onyx.app/self-hosted-llm-leaderboard](https://onyx.app/self-hosted-llm-leaderboard)  
> 26. The best open source large language model - Baseten, accessed on March 25, 2026, [https://www.baseten.co/blog/the-best-open-source-large-language-model/](https://www.baseten.co/blog/the-best-open-source-large-language-model/)  
> 27. Best LLMs for coding in 2026 - Builder.io, accessed on March 25, 2026, [https://www.builder.io/blog/best-llms-for-coding](https://www.builder.io/blog/best-llms-for-coding)  
> 28. DeepSeek Prover V2 vs Qwen3 Coder 480B A35B - AI Model Comparison - OpenRouter, accessed on March 25, 2026, [https://openrouter.ai/compare/deepseek/deepseek-prover-v2/qwen/qwen3-coder](https://openrouter.ai/compare/deepseek/deepseek-prover-v2/qwen/qwen3-coder)  
> 29. DeepSeek-Coder-V2 vs. Qwen3-Coder Comparison - SourceForge, accessed on March 25, 2026, [https://sourceforge.net/software/compare/DeepSeek-Coder-V2-vs-Qwen3-Coder/](https://sourceforge.net/software/compare/DeepSeek-Coder-V2-vs-Qwen3-Coder/)  
> 30. 10 Best Open-Source LLM Models (2025 Updated): Llama 4, Qwen 3 and DeepSeek R1, accessed on March 25, 2026, [https://huggingface.co/blog/daya-shankar/open-source-llms](https://huggingface.co/blog/daya-shankar/open-source-llms)  
> 31. Best Local LLM Models 2026 | Developer Comparison - SitePoint, accessed on March 25, 2026, [https://www.sitepoint.com/best-local-llm-models-2026/](https://www.sitepoint.com/best-local-llm-models-2026/)  
> 32. Qwen 3: Features, DeepSeek-R1 Comparison, Access, and More - DataCamp, accessed on March 25, 2026, [https://www.datacamp.com/blog/qwen3](https://www.datacamp.com/blog/qwen3)  
> 33. Deepseek V3 benchmarks are a reminder that Qwen 2.5 72B is the real king and everyone else is joking! : r/LocalLLaMA - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1hmqpca/deepseek\_v3\_benchmarks\_are\_a\_reminder\_that\_qwen/](https://www.reddit.com/r/LocalLLaMA/comments/1hmqpca/deepseek_v3_benchmarks_are_a_reminder_that_qwen/)  
> 34. The 11 best open-source LLMs for 2025 - n8n Blog, accessed on March 25, 2026, [https://blog.n8n.io/open-source-llm/](https://blog.n8n.io/open-source-llm/)  
> 35. Best Open Source LLMs: Complete 2026 Guide | Contabo Blog, accessed on March 25, 2026, [https://contabo.com/blog/open-source-llms/](https://contabo.com/blog/open-source-llms/)  
> 36. These Open-Source AI Agents Are INSANE! (Best of 2025) 🤯, accessed on March 25, 2026, [https://www.youtube.com/watch?v=8qGWRUGH5qo](https://www.youtube.com/watch?v=8qGWRUGH5qo)  
> 37. Best Small Language Models (March 2026): Run AI on 4GB RAM — Phi-4, Gemma 3, Qwen 3 | Local AI Master, accessed on March 25, 2026, [https://localaimaster.com/blog/small-language-models-guide-2026](https://localaimaster.com/blog/small-language-models-guide-2026)  
> 38. Why Small LLMs Are the Future of AI in 2025 | sanj.dev, accessed on March 25, 2026, [https://sanj.dev/post/small-llms-are-the-future](https://sanj.dev/post/small-llms-are-the-future)  
> 39. Top 5 Best LLM Models to Run Locally in CPU (2025 Edition) - Kolosal AI, accessed on March 25, 2026, [https://www.kolosal.ai/blog-detail/top-5-best-llm-models-to-run-locally-in-cpu-2025-edition](https://www.kolosal.ai/blog-detail/top-5-best-llm-models-to-run-locally-in-cpu-2025-edition)  
> 40. The Best Open-Source Small Language Models (SLMs) in 2026 - BentoML, accessed on March 25, 2026, [https://www.bentoml.com/blog/the-best-open-source-small-language-models](https://www.bentoml.com/blog/the-best-open-source-small-language-models)  
> 41. The 10 Best Self-Hosted AI Models You Can Run at Home - DreamHost, accessed on March 25, 2026, [https://www.dreamhost.com/blog/open-source-ai/](https://www.dreamhost.com/blog/open-source-ai/)  
> 42. The 2025 Ultimate List of Uncensored AI Models - Unrestricted & Free, accessed on March 25, 2026, [https://kextcache.com/uncensored-ai-models/](https://kextcache.com/uncensored-ai-models/)  
> 43. Top 10 LLMs with No Restrictions in 2026 - Apidog, accessed on March 25, 2026, [https://apidog.com/blog/llms-no-restrictions/](https://apidog.com/blog/llms-no-restrictions/)  
> 44. Best Uncensored AI Image Generators 2026 - Top NSFW AI Tools Ranked - OutreachZ, accessed on March 25, 2026, [https://outreachz.com/blog/best-uncensored-a-image-generators/](https://outreachz.com/blog/best-uncensored-a-image-generators/)  
> 45. What is the best uncensored Image to Image and Image to video generator for Windows : r/StableDiffusion - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/StableDiffusion/comments/1pg8psj/what\_is\_the\_best\_uncensored\_image\_to\_image\_and/](https://www.reddit.com/r/StableDiffusion/comments/1pg8psj/what_is_the_best_uncensored_image_to_image_and/)  
> 46. What is your current favorites realistic images checkpoint? : r/StableDiffusion - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/StableDiffusion/comments/1pgsw96/what\_is\_your\_current\_favorites\_realistic\_images/](https://www.reddit.com/r/StableDiffusion/comments/1pgsw96/what_is_your_current_favorites_realistic_images/)  
> 47. best \*UPDATED\* REALISM SDXL checkpoints??? : r/StableDiffusion - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/StableDiffusion/comments/1hg3atp/best\_updated\_realism\_sdxl\_checkpoints/](https://www.reddit.com/r/StableDiffusion/comments/1hg3atp/best_updated_realism_sdxl_checkpoints/)  
> 48. nsfw AI Models - Civitai, accessed on March 25, 2026, [https://civitai.com/tag/nsfw](https://civitai.com/tag/nsfw)  
> 49. Best AI Generators With No Restrictions in 2026 (Ranked) - RankZ, accessed on March 25, 2026, [https://rankz.co/blog/best-ai-generators-with-no-restrictions/](https://rankz.co/blog/best-ai-generators-with-no-restrictions/)  
> 50. Top 10 Vision Language Models in 2026 | Benchmark, Use Cases, accessed on March 25, 2026, [https://dextralabs.com/blog/top-10-vision-language-models/](https://dextralabs.com/blog/top-10-vision-language-models/)  
> 51. Introduction of InternVL2.5 Series - InternVL's tutorials! - Read the Docs, accessed on March 25, 2026, [https://internvl.readthedocs.io/en/latest/internvl2.5/introduction.html](https://internvl.readthedocs.io/en/latest/internvl2.5/introduction.html)  
> 52. This Open-Source AI Video Model Just Crushed Sora - YouTube, accessed on March 25, 2026, [https://www.youtube.com/watch?v=Xe0YmwcS0O4](https://www.youtube.com/watch?v=Xe0YmwcS0O4)  
> 53. Top open-source text-to-video AI models - Modal, accessed on March 25, 2026, [https://modal.com/blog/text-to-video-ai-article](https://modal.com/blog/text-to-video-ai-article)  
> 54. Top 5 Open Source Video Generation Models : r/learnmachinelearning - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/learnmachinelearning/comments/1ok4c2t/top\_5\_open\_source\_video\_generation\_models/](https://www.reddit.com/r/learnmachinelearning/comments/1ok4c2t/top_5_open_source_video_generation_models/)  
> 55. HunyuanVideo 1.5 : AI Video Generation in Consumer GPU | by Mehul Gupta | Data Science in Your Pocket | Medium, accessed on March 25, 2026, [https://medium.com/data-science-in-your-pocket/hunyuanvideo-1-5-ai-video-generation-in-consumer-gpu-a5d431589837](https://medium.com/data-science-in-your-pocket/hunyuanvideo-1-5-ai-video-generation-in-consumer-gpu-a5d431589837)  
> 56. Video Generation Comparison | Guides - Clore.ai, accessed on March 25, 2026, [https://docs.clore.ai/guides/comparisons/video-gen-comparison](https://docs.clore.ai/guides/comparisons/video-gen-comparison)  
> 57. Ultimate Guide - The Best Open Source Audio Generation Models in 2026 - SiliconFlow, accessed on March 25, 2026, [https://www.siliconflow.com/articles/en/best-open-source-audio-generation-models](https://www.siliconflow.com/articles/en/best-open-source-audio-generation-models)  
> 58. The Best Open-Source Text-to-Speech Models in 2026 - BentoML, accessed on March 25, 2026, [https://www.bentoml.com/blog/exploring-the-world-of-open-source-text-to-speech-models](https://www.bentoml.com/blog/exploring-the-world-of-open-source-text-to-speech-models)  
> 59. Best open source speech-to-text (STT) model in 2026 (with benchmarks) | Blog - Northflank, accessed on March 25, 2026, [https://northflank.com/blog/best-open-source-speech-to-text-stt-model-in-2026-benchmarks](https://northflank.com/blog/best-open-source-speech-to-text-stt-model-in-2026-benchmarks)  
> 60. Best Open Source LLMs in 2025: Top Models for AI Innovation | TRG Datacenters, accessed on March 25, 2026, [https://www.trgdatacenters.com/resource/best-open-source-llms-2025/](https://www.trgdatacenters.com/resource/best-open-source-llms-2025/)  
> 61. The Top 10 Medical Large Language Models of 2025: A Deep Dive into Performance, Safety, and Application - Dr7.ai, accessed on March 25, 2026, [https://dr7.ai/blog/health/automobile-all-the-stats-facts-and-data-youll-ever-need-to-know/](https://dr7.ai/blog/health/automobile-all-the-stats-facts-and-data-youll-ever-need-to-know/)  
> 62. axolotl vs unsloth \[performance and everything\] : r/LocalLLaMA - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1mltobj/axolotl\_vs\_unsloth\_performance\_and\_everything/](https://www.reddit.com/r/LocalLLaMA/comments/1mltobj/axolotl_vs_unsloth_performance_and_everything/)  
> 63. Best frameworks for fine-tuning LLMs in 2025 - Modal, accessed on March 25, 2026, [https://modal.com/blog/fine-tuning-llms](https://modal.com/blog/fine-tuning-llms)  
> 64. Axolotl vs Unsloth vs TorchTune: Best LLM Fine-Tuning Frameworks in 2026 | Spheron Blog, accessed on March 25, 2026, [https://www.spheron.network/blog/axolotl-vs-unsloth-vs-torchtune/](https://www.spheron.network/blog/axolotl-vs-unsloth-vs-torchtune/)  
> 65. Unsloth - Train and Run Models Locally, accessed on March 25, 2026, [https://unsloth.ai/](https://unsloth.ai/)
