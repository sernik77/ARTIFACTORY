---
title: Self-Hostable AI Models within 32 GB VRAM (Mar 2026)
kind: research
status: stable
summary: Which self-hostable AI models fit in 32 GB VRAM as of March 2026 and how they compare.
tags: [local-ai, self-hosting, vram, llm]
created: 2026-03-25
updated: 2026-03-25
origin: "https://docs.google.com/document/d/1qW1W5NXyvU9kRCqqBgNLyeeq0viKuBu0hiqLDthpAXs"
---

# **HIGH-THROUGHPUT ANALYSIS: SELF-HOSTABLE AI MODELS WITHIN 32 GB VRAM (MARCH 2026)**

## **ARCHITECTURAL PARADIGMS AND HARDWARE CONSTRAINTS IN 2026**

The landscape of local, self-hosted artificial intelligence in early 2026 is defined by a rapid transition from dense transformer networks to highly optimized Mixture-of-Experts (MoE) and hybrid state-space architectures.1 For hardware configurations limited to a maximum of 32 GB of Video RAM (VRAM), achieving frontier-level performance necessitates a delicate balance between total parameter count, active parameter count, and quantization methodologies.

Models deploying MoE architectures—such as Alibaba’s Qwen 3.5 35B-A3B—store large total parameter weights on disk but activate only a small fraction (e.g., 3 billion) during inference. This decoupling allows consumer hardware to output high-fidelity intelligence at inference speeds previously restricted to datacenter clusters.2 However, the total weights still require residency in VRAM, making aggressive quantization via formats like GGUF (Q4\_K\_M, Q5\_K\_M) or EXL2 an absolute requirement for 32 GB environments.4 Furthermore, the introduction of native FP8 and NVFP4 formats on next-generation hardware (such as the RTX 50-series) has reduced VRAM footprints by up to 60%, fundamentally altering deployment strategies for visual and multimodal tasks.6

The following analysis categorizes and evaluates the optimal open-weight models available as of March 2026, strictly filtered for viability within the 32 GB VRAM boundary across varying context lengths.

## ---

**1. UNCENSORED / LEAST CENSORED MODELS**

The pursuit of unfiltered artificial intelligence is driven by the necessity for unbiased research, unrestricted code generation, and hypothesis testing in sensitive domains without interference from Reinforcement Learning from Human Feedback (RLHF) guardrails. In 2026, the methodology for achieving this has shifted from simple dataset curation to "abliteration"—a process that surgically erodes safety alignments directly within the model's neural weights, bypassing predefined moral or safety constraints without destroying logical coherence.7

Dolphin 3.0, built upon the Llama 3.1 8B base architecture, stands as a precision-driven powerhouse in this category. Curated by Cognitive Computations, it employs a hybrid architecture heavily fine-tuned for structured reasoning and steerability. The primary operational advantage of Dolphin 3.0 lies in its exceptional compliance with system prompts; it acts purely as an extension of the user's instructions without imposing external ethical guidelines.7 This makes it exceptionally valuable for automated analysis pipelines where refusal mechanisms would break the workflow. In evaluations, it consistently outpaces peers in logic-intensive tasks, scoring above 80% on MMLU benchmarks.7 However, architectural limitations inherent to its dense 8B structure manifest as a highly direct prose style. It lacks narrative flair, prioritizing technical precision over creative storytelling, and its over-compliance means it will execute malicious code requests without hesitation.7

Nous Hermes 3, another Llama 3.1 8B derivative developed by NousResearch, occupies the opposite end of the uncensored spectrum. It is deeply optimized for creative writing, fiction crafting, and maintaining strict character consistency during long-term role-play. Utilizing the ChatML formatting structure, it exceeds 85% in targeted role-play evaluations.7 The model's training on diverse, unfiltered narrative datasets allows for deep exploration in storytelling and game design prototyping.7 Conversely, the model suffers from verbosity when concise technical answers are required, and it scores markedly lower on rigorous scientific benchmarks like GPQA (6.38%).7

For users requiring massive context windows without censorship, the LLaMA-3.2 Dark Champion Abliterated model utilizes an 8x3B MoE architecture to provide a 128,000-token context window. This model achieves retrieval accuracy benchmarks surpassing 90%, making it ideal for processing vast, unfiltered document sets.7 The abliteration process ensures unaligned outputs, but the model's memory footprint is massive. While it technically requires around 40 GB of VRAM in unquantized states, aggressive quantization to Q3\_K or Q4\_K allows it to squeeze into 32 GB, albeit with a slight degradation in perplexity.7

## **Category 1: Parameter and Capability Synthesis**

| Model | Dolphin 3.0 (Llama 3.1 8B Base) | Nous Hermes 3 | LLaMA-3.2 Dark Champion Abliterated |
| :---- | :---- | :---- | :---- |
| **Model Params** | 8B (Dense) | 8B (Dense) | 24B Total (8x3B MoE) |
| **Size on Disk** | ~5.0 GB (Q4\_K\_M) | ~5.0 GB (Q4\_K\_M) | ~14.5 GB (Q4\_K\_M) |
| **VRAM Usage** | 6GB (8k) / 8GB (32k) / 16GB (128k) | 6GB (8k) / 9GB (32k) | 16GB (8k) / 24GB (32k) / 32GB+ (128k) |
| **Quant Sweet Spot** | GGUF Q4\_K\_M (Speed) / Q8\_0 (Logic) | GGUF Q5\_K\_M (Nuance) | GGUF Q4\_K\_M (Fit constraint) |
| **Tool Use** | Yes (Native function calling) | Yes (via Github implementations) | No |
| **Context Length** | 128,000 tokens | 128,000 tokens (Peaks at 8k) | 128,000 tokens |
| **Best For** | Automated analysis pipelines, coding | Uncensored narrative generation | Unrestricted massive document RAG |
| **Major Pros** | High MMLU (>80%); strict steerability | Superior role-play (>85%); deep personas | 90%+ retrieval accuracy at high context |
| **Major Cons** | Dry prose; executes malicious code | Verbose; weak logic (6.38% GPQA) | Requires heavy quantization for 32GB |
| **License** | Llama 3.1 Community License | Llama 3.1 Community License | Custom / Abliterated |
| **Ecosystem / Tools** | Ollama, vLLM, LM Studio | HuggingFace, vLLM, SillyTavern | llama.cpp, text-generation-webui |
| **Recency** | February 2025 | September 2025 | Late 2025 / Early 2026 |

## ---

**2. EASE OF MODIFICATION AND FINE-TUNING / TRAINING**

The ability to self-host models extends beyond inference; the capacity to adapt a model to proprietary, localized datasets via Low-Rank Adaptation (LoRA) or Quantized LoRA (QLoRA) is paramount for enterprise deployment. In 2026, the ecosystem is heavily supported by frameworks like Unsloth and LlamaFactory, which drastically reduce the VRAM required to compute gradients and optimizer states.9

The Qwen 3.5 family (spanning 0.8B to 35B-A3B) represents the pinnacle of fine-tuning accessibility. Alibaba engineered the suite with a highly scalable foundation that natively supports 201 languages, making it the premier choice for localized multilingual applications.11 Integration with Unsloth allows Qwen 3.5 models to train 1.5 times faster while consuming 50% less VRAM compared to standard FlashAttention-2 setups. For example, a BF16 LoRA fine-tune on the Qwen3.5-9B model consumes only 22 GB of VRAM, sitting comfortably within a 24 GB or 32 GB hardware envelope.11 However, a critical constraint has emerged with the 2026 MoE architectures: 4-bit QLoRA training is explicitly not recommended for Qwen 3.5 MoE models. The mathematical divergence introduced by 4-bit quantization degrades the delicate expert routing mechanisms, necessitating 16-bit or 8-bit fine-tuning workflows.11 Additionally, developers must actively prompt the smaller models to disable internal "thinking" loops to prevent dataset contamination during supervised fine-tuning.12

Google's Gemma 3 models (12B and 27B variants) offer a highly portable alternative for single-accelerator fine-tuning. Released in early 2026, these models provide Day-0 integration with LlamaFactory for multimodal supervised fine-tuning.10 The architecture's massive 256,000-entry vocabulary and advanced RoPE (Rotary Position Embeddings) scaling—where base frequencies are pushed to 1,000,000—allow developers to easily fine-tune the models for custom document retrieval tasks spanning up to 128,000 tokens.14 The primary drawback of the Gemma 3 architecture during training is the massive embedding layer parameter count; the vocabulary overhead significantly increases the VRAM baseline, meaning a 27B QLoRA fine-tune requires nearly the entirety of a 32 GB VRAM buffer, leaving little room for batch size scaling.14

Meta’s Llama 4 Scout (17B-16E) presents an advanced MoE paradigm for institutional researchers. It features 17 billion active parameters out of 109 billion total, utilizing early fusion training for native multimodality.16 The model provides automatic support for tensor-parallel and device mapping in Hugging Face’s Transformers library.18 However, the sheer size of the total parameter weights (109B) makes traditional LoRA fine-tuning impossible on a single 32 GB GPU. Researchers must utilize extreme int4 quantization or distribute the training across multi-GPU setups (e.g., dual RTX 4090s or Apple M4 Max with 128GB unified memory).16

## **Category 2: Parameter and Capability Synthesis**

| Model | Qwen 3.5 (9B Dense) | Gemma 3 (12B) | Llama 4 Scout (17B Active / 109B Total) |
| :---- | :---- | :---- | :---- |
| **Model Params** | 9B (Dense) | 12B (Dense) | 109B Total (17B Active MoE) |
| **Size on Disk** | ~6.5 GB (Q4\_K\_M) | ~8.0 GB (Q4\_K\_M) | ~60 GB (Int4) |
| **Training VRAM** | ~22 GB for BF16 LoRA | ~20 GB for 8-bit QLoRA | Multi-GPU required for LoRA |
| **Quant Sweet Spot** | BF16 or 8-bit (Avoid 4-bit for MoE) | GGUF Q5\_K\_M / 8-bit training | Int4 (Inference only on 32GB) |
| **Tool Use** | Yes | Yes (Native function calling) | Yes |
| **Context Length** | 256,000 tokens | 128,000 tokens | Up to 10M tokens (hardware bounded) |
| **Best For** | Multilingual fine-tuning, RL tuning | Vision-language fine-tuning, RAG | Multi-node institutional research |
| **Major Pros** | 50% less VRAM via Unsloth; 201 languages | Native multimodal; 1M RoPE scaling | Early fusion multimodality; high capacity |
| **Major Cons** | 4-bit QLoRA breaks routing; kernel delays | High VRAM tax from 256k vocabulary | Total weights exceed 32GB limit |
| **License** | Apache 2.0 | Gemma License | Custom Llama 4 Community License |
| **Ecosystem / Tools** | Unsloth, LlamaFactory, Axolotl | LlamaFactory, Hugging Face Hub | LlamaFactory, TRL, Transformers v4.51 |
| **Recency** | March 2026 | Early 2026 | April 2025 / Updated 2026 |

## ---

**3. REASONING AND CHAIN-OF-THOUGHT**

The most significant architectural evolution of late 2025 and early 2026 is the mainstream adoption of internal monologue generation, or Chain-of-Thought (CoT) reasoning. These models utilize specialized \<think> tags to derive multi-step logical proofs before returning an answer, fundamentally altering benchmark scoring in complex mathematics and scientific reasoning.20

NVIDIA Nemotron 3 Nano (30B A3B) is an unprecedented achievement in specialized logical architectures. By combining Mamba-2 state-space models with standard Transformer MoE layers, it achieves an astonishing 91.0% on the AIME 2025 mathematical benchmark, entirely dominating the 30B weight class.1 Its prowess extends into high-level academia, scoring 75.7% on the GPQA Diamond (PhD-level science) benchmark.1 The architecture allows users to explicitly toggle reasoning traces on or off, providing granular control over inference latency.2 However, this hybrid state-space architecture struggles with conventional Key-Value (KV) cache management in tools like Ollama. As context approaches 48,000 tokens, memory overhead forces offloading to system RAM, plummeting generation speeds to 11.45 tokens per second (t/s).1 Furthermore, its general software coding index (19.0%) trails significantly behind specialized agentic models.1

DeepSeek R1 Distill Qwen 32B represents the distillation of the massive 671B DeepSeek-R1-Zero reinforcement learning (RL) logic into a dense 32B parameter chassis.23 The model inherits the exact RL-trained internal monologue structures of its larger sibling, making it highly methodical. It dominates specific logic tests, achieving a 94.1% on Math-500.1 Operating as a dense transformer, it sidesteps the massive VRAM footprint penalty associated with the total weights of MoE models, fitting securely within 24-32 GB VRAM environments.23 The distillation process, however, is imperfect. The model occasionally falls into infinite repetition loops when presented with paradoxical prompts, and its overall broad intelligence index (17.2%) indicates that it traded general knowledge for narrow mathematical supremacy.1

GLM-4.7-Flash, developed by Zhipu AI, offers a distinct approach to reasoning through its "Interleaved Thinking" paradigm. Unlike models that think only once per prompt, GLM-4.7 performs a mandatory chain-of-thought before every single tool call or sub-action within an agentic workflow.21 This meticulous approach earned it the highest overall Intelligence Index (30.1%) in the 24GB hardware testing suite.1 It also features a "Retention-Based" monologue control that preserves thinking blocks across multi-turn sessions, eliminating data loss during long-horizon tasks.21 The fatal flaw of this deep reasoning is the "Context Cliff." The internal monologues consume vast amounts of the context window. On a 24GB or 32GB GPU, the model operates swiftly at low context (42.47 t/s) but suffers total VRAM exhaustion past 48,000 tokens, collapsing to 6.57 t/s as it bleeds into DDR5 system RAM.1

## **Category 3: Parameter and Capability Synthesis**

| Model | NVIDIA Nemotron 3 Nano (30B A3B) | DeepSeek R1 Distill Qwen 32B | GLM-4.7-Flash |
| :---- | :---- | :---- | :---- |
| **Model Params** | 30B Total (3.5B Active) Hybrid MoE | 32B (Dense) | 30B Total (3B Active) MoE |
| **Size on Disk** | ~19 GB (Q4\_K\_M) | ~18.5 GB (Q4\_K\_M) / ~26 GB (Q6) | ~19 GB (Q4\_K\_M) |
| **VRAM Usage** | 24GB base; heavy KV overhead | 19GB (8k) / 26GB (32k) / 39GB (FP16) | 19GB base; hits cliff at 48k |
| **Quant Sweet Spot** | GGUF Q5\_K\_M (Mamba precision) | EXL2 5.0 bpw / GGUF Q6 | GGUF Q4\_K\_M |
| **Tool Use** | Yes | Moderate (Prone to logic loops) | Exceptional (87.4% τ²-Bench) |
| **Context Length** | 1,000,000 tokens nominal | 128,000 tokens | 198,000 tokens nominal |
| **Best For** | PhD-level science (GPQA), Math | Step-by-step mathematical logic | Agentic reasoning, multi-turn code |
| **Major Pros** | 91.0% AIME 2025; togglable logic | 94.1% Math-500; RL logic paths | Interleaved thinking; high Intel Index |
| **Major Cons** | Drops to 11 t/s at 48k context | Infinite \<think> loops; weak coding | Severe VRAM exhaustion past 48K |
| **License** | NVIDIA Open Model License | MIT | MIT |
| **Ecosystem / Tools** | Ollama, TensorRT-LLM | vLLM, llama.cpp, ExLlamaV2 | Ollama (v0.14.3+), vLLM |
| **Recency** | March 2026 | Early 2026 | January 2026 |

## ---

**4. MODEL SIZE (SMALLEST MODELS)**

The optimization of Small Language Models (SLMs) in 2026 proves that sub-5B parameter architectures can execute highly complex workflows, provided they are fine-tuned on hyper-curated synthetic datasets. These models are designed for edge devices, embedded systems, and extreme low-latency environments.

Microsoft’s Phi-4-mini Reasoning (3.8B) redefines the capabilities of a compact decoder-only transformer. Fine-tuned on a blend of high-quality synthetic STEM data and OpenAI's o3-mini derivations, it systematically outperforms models twice its size in strict logical reasoning.24 It supports a massive native context window of 128,000 tokens and leverages custom \<think> tags to enforce structured reasoning formatting.24 Due to its 3.8B size, it can be executed in high-precision Q8\_0 quantization while occupying less than 6 GB of VRAM, preserving extreme accuracy.27 The primary trade-off is a profound lack of broad world knowledge; its low parameter count means it struggles heavily with factual recall outside of its provided context. Furthermore, it is strictly optimized for the English language and adheres to rigid safety alignments.25

Alibaba’s Qwen 3.5 4B takes an alternate route, focusing on multimodal versatility and linguistic breadth rather than pure mathematical logic. Utilizing an early fusion architecture, this 4-billion parameter dense model supports 201 languages and processes image inputs natively alongside text.12 It requires only 3.49 GB of VRAM when quantized to Q4, making it highly deployable on entry-level hardware.29 However, the breadth of its linguistic training within such a small parameter space causes it to suffer from hallucination spikes, particularly when tasked with generating syntax in obscure programming languages.12 It lacks the deep, reinforced chain-of-thought architecture of its reasoning-specific peers.

## **Category 4: Parameter and Capability Synthesis**

| Model | Phi-4-mini Reasoning (3.8B) | Qwen 3.5 (4B) |
| :---- | :---- | :---- |
| **Model Params** | 3.8B (Dense) | 4B (Dense) |
| **Size on Disk** | ~2.5 GB (Q4) / ~4.0 GB (Q8\_0) | ~2.8 GB (Q4\_K\_M) |
| **VRAM Usage** | 3.5GB (8k) / 6GB (128k) | 3.5GB (Q4) / 10.6GB (FP16) |
| **Quant Sweet Spot** | Q8\_0 (Preserves logic in low RAM) | Q6\_K |
| **Tool Use** | Moderate | Yes |
| **Context Length** | 128,000 tokens | 256,000 tokens |
| **Best For** | Low-latency QA, text-based edge processing | Multimodal translation on 8GB laptops |
| **Major Pros** | Outperforms 8B models in math; 128k window | Natively multimodal; 201 languages supported |
| **Major Cons** | Poor factual recall; strictly English focused | Syntax hallucinations; weak deep reasoning |
| **License** | MIT | Apache 2.0 |
| **Ecosystem / Tools** | ONNX, llama.cpp, Ollama | vLLM, LM Studio |
| **Recency** | December 2024 / Updated 2026 | March 2026 |

## ---

**5. RAW HARDWARE REQUIREMENTS (LOWEST)**

For deployments on hardware entirely lacking modern discrete GPUs—such as legacy laptops, Raspberry Pi clusters, or systems with 8 GB of unified memory—models must be optimized for CPU execution and extreme quantization without catastrophic performance degradation.27

DeepSeek-R1-Distill-Qwen-1.5B is currently the absolute smallest viable reasoning engine on the market. Distilled directly from the 671B DeepSeek-R1-Zero into a minuscule 1.5B parameter Qwen chassis, it manages to retain basic \<think> tag logic for rudimentary mathematical proofs.23 Because the FP16 (unquantized) model requires only 3.9 GB of memory, it entirely bypasses the need for quantization, preventing the precision loss that normally plagues micro-models.23 It achieves over 20 tokens per second purely on DDR5 system RAM using llama.cpp.23 The model is, however, completely incapable of factual recall; it must be fed all necessary data via the prompt, and its reasoning degrades immediately when processing non-English or non-Chinese inputs.23

Mistral Small 3 (7B) is engineered specifically as the "sweet spot" for standard 16 GB RAM laptops. Running at a Q4\_K\_M quantization, the weights consume approximately 4.8 GB of RAM, leaving enough overhead for operating systems and basic Docker containers without triggering Out-of-Memory (OOM) fatal crashes.30 The model excels in speed, achieving roughly 30 t/s on CPU-only execution while maintaining a highly reliable coding profile (68.2 on HumanEval).30 While it lacks the creative refactoring capabilities of larger 14B models, its dense architecture provides a much smoother execution profile on limited hardware than MoE models, which require constant memory swapping.30

## **Category 5: Parameter and Capability Synthesis**

| Model | DeepSeek-R1-Distill-Qwen-1.5B | Mistral Small 3 (7B) |
| :---- | :---- | :---- |
| **Model Params** | 1.5B (Dense) | 7B (Dense) |
| **Size on Disk** | ~1.1 GB (Q4) / ~3.0 GB (FP16) | ~4.8 GB (Q4\_K\_M) |
| **VRAM / RAM Usage** | \<4.0 GB total system requirement | ~5.5 GB base memory footprint |
| **Quant Sweet Spot** | FP16 (No quantization needed) | GGUF Q4\_K\_M |
| **Tool Use** | No | Yes (Native function calling) |
| **Context Length** | 32,000 tokens | 32,000 tokens |
| **Best For** | Embedded IoT logic, CPU-only servers | "Out of the box" laptop coding assistant |
| **Major Pros** | Micro-footprint; retains CoT tags | Perfect fit for 16GB RAM; 30 t/s on CPU |
| **Major Cons** | Zero factual recall; strict language limits | Less creative than 14B competitors |
| **License** | MIT | Apache 2.0 |
| **Ecosystem / Tools** | llama.cpp, GPT4All | LM Studio, Ollama |
| **Recency** | Early 2026 | Early 2026 |

## ---

**6. HARDWARE REQUIREMENTS TO CAPABILITY RATIO (BEST VALUE)**

This category evaluates the highest intelligence quotient achieved per gigabyte of VRAM utilized. The optimal models here maximize the 24 GB to 32 GB envelopes by utilizing advanced architectural tricks rather than brute force parameter scaling.

Qwen 3.5 35B-A3B is the definitive standard for VRAM efficiency in 2026. This Sparse MoE model features 35 billion total parameters, ensuring a massive repository of world knowledge and complex linguistic routing, yet it activates a mere 3 billion parameters during token generation.3 This allows it to outcompete dense 32B models in reasoning while executing at the inference speed of a 3B model.2 On a 24GB GPU, it acts as the "stability king," maintaining 100% GPU utilization at a 48,000-token context depth with zero offloading to system RAM.1 It is also natively multimodal, negating the need to load secondary vision models.32 The caveat is its footprint on disk; the 35B total weights require ~21.5 GB of VRAM just to load in Q4 precision, completely isolating users with 8 GB or 12 GB GPUs.29 Furthermore, it is highly sensitive to quantization methodologies—using Unsloth Dynamic quants (UD-Q4\_K\_XL) inadvertently increases perplexity by nearly 10%, necessitating the use of standard Q4\_K\_M.33

Google's Gemma 3 (27B) represents the pareto-optimal point for dense architectures. Google engineered this model specifically to outpace larger 70B models (including Gemini 1.5-Pro on select benchmarks) while remaining viable for single-accelerator deployment.34 Its multimodal integration provides unmatched image-to-text accuracy among open-weight models of this size.34 The model's 1,000,000 RoPE base frequency scaling allows flawless data retrieval across a massive 128,000 token context window.13 However, the dense 27B structure exacts a heavy memory bandwidth toll. It requires highly parallel VRAM (GDDR6X or unified Apple Silicon) to maintain tokens-per-second; executing it across PCI-e to DDR5 system RAM results in massive latency spikes.14

## **Category 6: Parameter and Capability Synthesis**

| Model | Qwen 3.5 35B-A3B | Gemma 3 (27B) |
| :---- | :---- | :---- |
| **Model Params** | 35B Total (3B Active) MoE | 27B (Dense) |
| **Size on Disk** | ~21.5 GB (Q4\_K\_M) | ~17 GB (Q4\_K\_M) |
| **VRAM Usage** | 21.5GB (Base) / 24GB (48k context) | 18GB (8k) / 22GB (32k) / 28GB (128k) |
| **Quant Sweet Spot** | Standard GGUF Q4\_K\_M (Avoid dynamic) | GGUF Q5\_K\_M |
| **Tool Use** | High (78.6% BrowseComp) | Yes (Native) |
| **Context Length** | 256,000 tokens | 128,000 tokens |
| **Best For** | Daily driver for 24-32GB VRAM; high speed | Single-accelerator multimodal enterprise RAG |
| **Major Pros** | Thinks like 35B, runs like 3B; stable context | Pareto optimal; beats larger models in vision |
| **Major Cons** | Requires 21.5GB just to load; quant sensitive | Heavy bandwidth toll on dense inference |
| **License** | Apache 2.0 | Gemma License (Commercial restrictions) |
| **Ecosystem / Tools** | llama.cpp, Ollama | Hugging Face, vLLM |
| **Recency** | March 2026 | Early 2026 |

## ---

**7. SPEED / INFERENCE SPEED**

Inference speed is dictated by memory bandwidth and active parameter count. The models in this tier are evaluated on Tokens Per Second (t/s) relative to their size, highlighting the distinct advantages of MoE routing over dense processing.33

Llama 3.3 (8B) utilizes Meta's highly optimized dense architecture, achieving blistering speeds that saturate consumer hardware. On a mid-tier RTX 3060, it effortlessly surpasses 70 t/s, and on 4090-class hardware, it exceeds 150 t/s.27 Despite its speed, it scores a highly respectable 73.0 on the MMLU benchmark at Q4\_K\_M quantization, effectively matching the capabilities of proprietary cloud APIs from 2024.31 The limitation of this speed-optimized architecture is its absolute ceiling in reasoning; it lacks the complex neural routing necessary to compete with 2026 MoE models on advanced logic or scientific problem solving.31

The speed profile of the Qwen 3.5 35B-A3B illustrates the MoE advantage. Benchmarked on an RTX 5080 (16GB GDDR7 with 960 GB/s bandwidth), the model achieves 67.4 t/s at short context and maintains a virtually identical 64.1 t/s even when the context window is expanded to 65,000 tokens.33 This minimal degradation (-3.3 t/s) over long documents proves the superiority of the Sparse MoE architecture. Because it only activates 3 billion parameters per token, the memory bus is never fully saturated by compute, leaving ample bandwidth to process massive KV cache requests rapidly.33

## **Category 7: Parameter and Capability Synthesis**

| Model | Llama 3.3 (8B) | Qwen 3.5 35B-A3B (Speed Profile) |
| :---- | :---- | :---- |
| **Model Params** | 8B (Dense) | 35B Total (3B Active) MoE |
| **Inference Speed** | >70 t/s (RTX 3060) / >150 t/s (RTX 4090) | ~67.4 t/s (Short) / ~64.1 t/s (65k Long Context) |
| **VRAM Usage** | ~6 GB | ~21.5 GB |
| **Quant Sweet Spot** | Q4\_K\_M | Auto-fit Q4\_K\_M |
| **Tool Use** | Yes | High |
| **Context Length** | 128,000 tokens | 256,000 tokens |
| **Best For** | Real-time chatbot backends; rapid data parsing | Massive document parsing at high velocity |
| **Major Pros** | Blistering speed; high MMLU for size | Near-zero speed degradation at massive context |
| **Major Cons** | Incapable of deep 2026-level reasoning | Requires 24GB+ hardware for optimal speed |
| **License** | Llama 3.3 Community License | Apache 2.0 |
| **Ecosystem / Tools** | vLLM, ExLlamaV2 | llama.cpp (cuBLAS) |
| **Recency** | Late 2025 | March 2026 |

## ---

**8. GENERAL CAPABILITY**

This category aggregates overall intelligence, coherence, instruction following, and the ability to multitask across disparate domains without specialized fine-tuning.1

GLM-4.7-Flash is the reigning generalist for the 24 GB to 32 GB hardware class. Beyond its reasoning capabilities, it achieved a 30.1% Intelligence Index score across a battery of multi-disciplinary tests.1 Its defining generalist feature is its aptitude for "Vibe Coding"—the ability to generate aesthetically superior UI components, modern responsive webpages, and highly accurate slide layouts based on vague human instructions.21 Its primary limitation remains its aggressive VRAM consumption; the model is practically unusable past 48,000 tokens on 24 GB GPUs due to context cliff memory offloading.1

Moonshot AI's Kimi K2.5 utilizes a unique sub-model architecture. While the foundational model scales up to 1 Trillion parameters, the self-hostable ecosystem allows the execution of an "Agent Swarm." Kimi can self-direct up to 100 sub-agents to operate in parallel, drastically reducing the wall-clock time required for general capabilities like complex research or multi-step planning.37 Using Parallel-Agent Reinforcement Learning (PARL), this architecture completes tasks up to 4.5x faster than single-agent models.37 The model is severely restricted by its commercial license, which requires mandatory UI branding if Monthly Active Users (MAU) exceed 100 million or revenue targets are hit.37

## **Category 8: Parameter and Capability Synthesis**

| Model | GLM-4.7-Flash | Kimi K2.5 (Swarm Architecture) |
| :---- | :---- | :---- |
| **Model Params** | 30B Total (3B Active) MoE | Scales dynamically via sub-agents |
| **VRAM Usage** | ~24 GB Max (Hard limits at 48K) | Dependent on swarm size configuration |
| **Quant Sweet Spot** | GGUF Q4\_K\_M | N/A (API / Backend configured) |
| **Tool Use** | Exceptional (87.4% τ²-Bench) | Highest (1,500 tool calls per trace) |
| **Context Length** | 198,000 tokens | 256,000 tokens |
| **Best For** | End-to-end coding; "Vibe" UI design | Massively parallel research and multi-step planning |
| **Major Pros** | Highest Intel Index (30.1%); modern UI coding | 4.5x faster task completion via agent swarms |
| **Major Cons** | Severe VRAM exhaustion at 48k context | Restrictive commercial branding license |
| **License** | MIT | Modified MIT (Branding required) |
| **Ecosystem / Tools** | Ollama, vLLM | Moonshot ecosystem |
| **Recency** | January 2026 | February 2026 |

## ---

**9. TOOL USE / AGENTic CAPABILITIES**

Evaluating a model's ability to act autonomously involves testing its reliability in interacting with operating systems, executing terminal commands, and browsing the web using benchmarks like τ²-Bench, OSWorld, and Terminal-Bench 2.0.20

The Qwen 3.5 suite exhibits absolute dominance in graphic operating system interactions and web browsing. On the OSWorld benchmark, the models score 62.2%, indicating a deep semantic understanding of desktop environments.20 On BrowseComp, which tests web research and browser automation, it achieves 78.6%.20 Overall, it scores an 86.7% on the ARC-AGI-2 benchmark, securing its place as a top-tier general agent.20 Its primary weakness in this category is its lower aptitude for raw Command Line Interface (CLI) operations, scoring only 52.5% on Terminal-Bench 2.0.20

Conversely, the GLM-5 and GLM-4.7 frameworks excel in raw technical and terminal-based tool use. GLM-5 achieves a commanding 56.2% on Terminal-Bench 2.0, dominating CLI operations.40 Furthermore, GLM-4.7 scores an 87.4% on τ²-Bench, a benchmark designed specifically to expose how brittle AI agents are when forced to manage multi-turn workflows without hallucinating payload inputs.21 This makes the GLM family the premier choice for backend server automation and DevOps agentic tasks.

## **Category 9: Parameter and Capability Synthesis**

| Model | Qwen 3.5 (Broad Family) | GLM-5 / GLM-4.7 |
| :---- | :---- | :---- |
| **Model Params** | Scalable (4B to 35B-A3B) | 30B to 744B MoE classes |
| **Tool Use Metrics** | OSWorld: 62.2% / BrowseComp: 78.6% | Terminal-Bench 2.0: 56.2% / τ²-Bench: 87.4% |
| **Context Length** | 256,000 tokens | Up to 200,000 tokens |
| **Best For** | Web browsing, graphic OS interaction | Backend automation, CLI execution, DevOps |
| **Major Pros** | Dominates browser automation and GUI tasks | Flawless multi-turn terminal command generation |
| **Major Cons** | Weaker at pure CLI terminal operations | Deep reasoning consumes vast context memory |
| **License** | Apache 2.0 | MIT |
| **Ecosystem / Tools** | LangChain, LlamaIndex integration | Claude Code, Kilo Code, Cline, Roo Code |
| **Recency** | March 2026 | Early 2026 |

## ---

**10. CODING / PROGRAMMING**

Code generation requires exact syntax, logical coherence, and the ability to debug across multiple files. This is evaluated using SWE-bench Verified (which tests real GitHub issue resolution) and LiveCodeBench (which tests against freshly generated, uncontaminated coding problems).20

DeepSeek-V3.2 (Lite/Distill) models are the undisputed leaders in open-weight programming. The architecture is engineered to prioritize coding pathways, achieving an 88.3% on LiveCodeBench and a 73.1% on SWE-bench Verified, placing it nearly on par with proprietary enterprise models like Claude Opus.41 However, replicating this performance locally requires managing the model's massive quantization requirements. When compressed to the Distill-32B variant to fit 24 GB VRAM, the SWE-bench score degrades significantly to 49.2%, exposing the fragility of coding logic when network precision is reduced.20

Qwen 3 Coder 30B A3B Instruct is the dedicated software engineering variant of the Qwen 3 line. It achieves a 19.4 Coding Index and scores 40.3% on LiveCodeBench, the highest verified score among models natively designed for the 30B weight class without heavy distillation loss.1 At a Q4\_K\_M quantization, it occupies approximately 21 GB of VRAM, making it a highly reliable, real-time IDE integration companion capable of resolving complex GitHub issues autonomously without exceeding the 32 GB hardware barrier.27

## **Category 10: Parameter and Capability Synthesis**

| Model | DeepSeek-V3.2 (Distill-32B) | Qwen 3 Coder 30B A3B Instruct |
| :---- | :---- | :---- |
| **Model Params** | 32B (Dense) | 30B Total (3B Active) MoE |
| **Size on Disk** | ~26 GB (Q6) | ~21 GB (Q4\_K\_M) |
| **VRAM Usage** | ~26 GB (32k context) | ~21 GB (32k context) |
| **Coding Benchmarks** | LiveCodeBench: 88.3% (Full) / SWE-Bench: 49.2% (Distill) | LiveCodeBench: 40.3% / Coding Index: 19.4 |
| **Tool Use** | Moderate | High |
| **Context Length** | 128,000 tokens | 128,000 tokens |
| **Best For** | Offline competitive programming | Real-time IDE integration, automated debugging |
| **Major Pros** | Elite coding logic matching proprietary models | Highly stable logic at 4-bit quantization |
| **Major Cons** | Severe performance drop in 32B distill variant | Lower ceiling than full DeepSeek implementations |
| **License** | MIT | Apache 2.0 |
| **Ecosystem / Tools** | vLLM, ExLlamaV2 | Ollama, Continue.dev, LM Studio |
| **Recency** | Early 2026 | March 2026 |

## ---

**11. UNIQUE AND SPECIAL ABILITIES**

This category highlights models trained explicitly for highly specialized domains, such as medical diagnostics, scientific theorem proving, and visual logic.

MedGemma 27B is Google's medically optimized multimodal SLM.45 Trained specifically on health records and medical imaging, it achieves a MedQA score of 87.7%, placing it within 3 points of massive closed-source medical models at a fraction of the computational cost.45 In clinical evaluations, 81% of the chest X-ray reports generated by MedGemma 27B were judged by board-certified cardiothoracic radiologists to be clinically accurate enough to dictate patient management.45 It requires ~24 GB of VRAM in Q4 precision, making it the premier choice for hospitals or researchers analyzing private healthcare data locally.45

Intellect 3 is a 106B-parameter model (requiring multi-GPU quantization to fit a 32GB threshold) built specifically for scientific and medical reasoning. Built on Z.ai's GLM 4.5 Air architecture, it diverges entirely from general reasoning engines to focus exclusively on formal mathematics, biological pathways, and scientific hypothesis generation.46

Qwen 3 VL 32B is the premier visual-logic model for 32 GB setups. Achieving an MMLU-Pro score of 81.8% and an Intelligence Index of 24.5, it seamlessly bridges the gap between text and high-resolution image analysis.1 It is unmatched in its ability to perform deep optical character recognition (OCR) on complex documents and execute logic based on visual UI inputs.1

## **Category 11: Parameter and Capability Synthesis**

| Model | MedGemma 27B | Intellect 3 | Qwen 3 VL 32B |
| :---- | :---- | :---- | :---- |
| **Model Params** | 27B (Dense) | 106B Total | 32B (Dense) |
| **VRAM Usage** | ~24GB (Q4) | Multi-GPU / Extreme Quantization | ~24GB (Q4) |
| **Specialization** | Medical diagnostics, X-Ray analysis | Scientific reasoning, formal math | Visual logic, high-res OCR |
| **Context Length** | 128,000 tokens | Up to 128,000 tokens | 128,000 tokens |
| **Best For** | Private healthcare data analysis | Scientific theorem proving | Image-to-code, document logic |
| **Major Pros** | 87.7% MedQA; 81% clinical accuracy rate | Specialized STF and RL for science | 81.8% MMLU-Pro; seamless UI logic |
| **Major Cons** | Requires strict system prompting for advice | Exceeds single 32GB VRAM natively | Dense architecture consumes high VRAM |
| **License** | Gemma License | Proprietary / Mixed | Apache 2.0 |
| **Recency** | Early 2026 | Early 2026 | March 2026 |

## ---

**MULTIMODAL CATEGORY 1: BEST SELF-HOSTABLE IMAGE GENERATION**

Image generation in 2026 is defined by the integration of natively supported low-precision formats (FP8 and NVFP4), allowing massive photorealistic models to run on standard consumer hardware without Out-of-Memory failures.6

FLUX.2 Klein (9B), developed by Black Forest Labs, represents the frontier of visual generative AI. The architecture is explicitly designed to eliminate the "AI look" by enforcing strict real-world lighting and physics models, rendering flawless text across infographics and UI designs.48 The 2026 iteration natively integrates NVFP4 and FP8 formats. On RTX 50-series GPUs, this reduces VRAM requirements by 60% and boosts generation speeds by 2.5x.6 The 9-billion parameter model requires only 12 to 16 GB of VRAM in FP8, making it a highly accessible, rapid-iteration tool for storyboarding in node-based workflows like ComfyUI.6

The legacy FLUX.1 (12B) remains highly capable for users willing to heavily quantize. At full FP16 precision, it demands 24 to 33 GB of VRAM, but utilizing a GGUF Q5 quantization drops the requirement to 8-10 GB while maintaining 95% of the visual fidelity, securing its place as the definitive budget option.47

## **Image Generation Parameter Synthesis**

| Model | FLUX.2 Klein (9B) | FLUX.1 (12B) |
| :---- | :---- | :---- |
| **Model Params** | 9 Billion | 12 Billion |
| **VRAM Usage** | ~12-16GB (FP8 / NVFP4) | 24-33GB (FP16) / 8-10GB (GGUF Q5) |
| **Quant Sweet Spot** | FP8 or NVFP4 | GGUF Q5 |
| **Best For** | High-fidelity storyboarding, UI rendering | Budget photorealism on 8GB GPUs |
| **Major Pros** | Flawless text rendering; 60% less VRAM | High quality preservation at Q5 |
| **Major Cons** | Requires modern GPU architecture for NVFP4 | Extremely heavy at full FP16 precision |
| **Ecosystem / Tools** | ComfyUI | ComfyUI, Forge UI |

## ---

**MULTIMODAL CATEGORY 2: BEST SELF-HOSTABLE VIDEO GENERATION**

Video generation models must balance spatial resolution, temporal consistency (frames per second), and aggressive VRAM limitations.

LTX 2.3 is the undisputed leader in local video generation. Utilizing a massive 22-billion parameter architecture compressed via NVFP4 formats, it generates 15 seconds of cinematic, temporally consistent 4K video using exactly 24 GB of VRAM.49 Operating within the optimized WanGP ecosystem, it boasts ridiculously low memory overhead, enabling desktop users to perform advanced video and audio editing locally.6

Wan 2.2 (14B), developed by Alibaba Tongyi Lab, utilizes a groundbreaking dual-expert MoE setup based on Signal-to-Noise Ratio (SNR). One expert processes the early, high-noise frames to establish layout and motion, while the second expert assumes control to finalize texture and lighting.51 Because only one 14B expert runs at any given time, the model achieves 27B-level aesthetics with half the compute.52 Using RIFLEx technology to prevent repetitive looping, the 14B model can generate 8 seconds of 720p video requiring only 12 GB of VRAM. For users on an absolute budget, it can output 5 seconds of 480p video on a mere 8 GB of VRAM.53

## **Video Generation Parameter Synthesis**

| Model | LTX 2.3 | Wan 2.2 (14B) |
| :---- | :---- | :---- |
| **Model Params** | 22 Billion | 14 Billion (Dual-Expert MoE) |
| **VRAM Usage** | 12GB (1080p) / 24GB (4K) | 8GB (480p) / 12GB (720p) |
| **Capabilities** | 15s 4K video generation | 8s 720p video generation via RIFLEx |
| **Best For** | Cinematic concept development at 4K | Aesthetic, directed clips on budget hardware |
| **Major Pros** | Class-leading temporal consistency | SNR-based MoE drastically cuts compute overhead |
| **Major Cons** | Requires 24GB VRAM for full 4K output | Lower max resolution than LTX |
| **Ecosystem / Tools** | ComfyUI, WanGP | ComfyUI, WanGP, Pinokio |

## ---

**STRATEGIC HARDWARE RECOMMENDATIONS AND ARCHITECTURAL CONCLUSIONS**

Based on the synthesized data, deploying open-weight models in 2026 requires strict alignment between the user's hardware tier, VRAM bandwidth, and the specific quantization constraints of the chosen architecture.

## **1. Lowest Requirements (CPU / 4–6 GB VRAM)**

Targeting older laptops, edge devices, and legacy GPUs (e.g., GTX 1660 Ti).

* **Text / Reasoning Engine**: **DeepSeek-R1-Distill-Qwen-1.5B**. It requires less than 4 GB of VRAM for full FP16 execution, bypassing quantization losses entirely while providing unparalleled \<think> logic for its size.23  
* **Code / General Agent**: **Qwen 3.5 4B**. Consuming only 3.49 GB VRAM in Q4 precision, it brings massive multilingual and multimodal capabilities to extreme budget hardware.29

## **2. Mid-Tier (8–12 GB VRAM)**

Targeting hardware such as the RTX 3060, RTX 4060, or Apple M2 with 16GB Unified Memory.

* **The "Daily Driver" (Text/Code)**: **Llama 3.3 8B (Q4\_K\_M)**. Operating at blistering speeds (>70 t/s), it perfectly fits the memory envelope while delivering reliable, high-tier instruction following.31  
* **Reasoning Specialist**: **Phi-4-mini Reasoning 3.8B (Q8\_0)**. Allows for maximum 8-bit precision reasoning within a 6 GB footprint.24  
* **Image / Video**: **FLUX.1 Q5** (8-10GB) and **Wan 2.2 5B**, which auto-offloads cleanly to system RAM while generating 720p video.47

## **3. High-End (16–24 GB VRAM)**

Targeting workstation GPUs like the RTX 4080, RTX 3090, and RTX 4090.

* **The "Daily Driver" (General & Speed)**: **Qwen 3.5 35B-A3B**. At Q4\_K\_M, it occupies ~21.5 GB VRAM, leaving room for a stable 32,000 token context window. It provides the intelligence of a 35B model but executes with the blistering inference speed of a 3B model, making it the supreme choice for this tier.29  
* **The Reasoning King**: **NVIDIA Nemotron 3 Nano 30B A3B**. Fits cleanly into 24 GB and crushes mathematical logic (91.0% AIME).2  
* **The Video Studio**: **LTX 2.3**. Will comfortably generate 4K 15s cinematic videos entirely within the 24 GB limit.49

## **4. Enthusiast / Workstation (Full 32 GB VRAM)**

Targeting ultimate local setups such as the RTX 5090 (32GB), Dual RTX 4080s, or Mac M4 Max 128GB Unified Memory.19

* **The Ultimate Coder / Reasoner**: **DeepSeek-R1-Distill-Qwen-32B at Q6 or Q8**. By utilizing 26 GB+ VRAM, this lighter quantization level ensures zero degradation in the delicate RL-trained \<think> logic loops required for massive Python refactoring.4  
* **The Multimodal Powerhouse**: **Gemma 3 27B**. Running at Q8 precision, it consumes ~28 GB VRAM but provides a massive 128,000 token context window and Google-tier vision analysis for complex RAG pipelines.27  
* **The Agentic Core**: **GLM-4.7-Flash (30B MoE)**. While it suffers a severe context cliff on 24 GB cards, a full 32 GB VRAM setup allows its "Interleaved Thinking" to stretch up to 100,000 tokens without hitting system RAM. This fully unleashes its 87.4% τ²-Bench tool-use mastery for complex backend server automation.1

#### **Works cited**

> 1. Best Local LLMs for 24GB VRAM: Performance Analysis 2026 ..., accessed on March 25, 2026, [https://localllm.in/blog/best-local-llms-24gb-vram](https://localllm.in/blog/best-local-llms-24gb-vram)  
> 2. What is the best general-purpose model to run locally on 24GB of VRAM in 2026? - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1qlwibf/what\_is\_the\_best\_generalpurpose\_model\_to\_run/](https://www.reddit.com/r/LocalLLaMA/comments/1qlwibf/what_is_the_best_generalpurpose_model_to_run/)  
> 3. Qwen3.5-35B-A3B: Specifications and GPU VRAM Requirements - ApX Machine Learning, accessed on March 25, 2026, [https://apxml.com/models/qwen35-35b-a3b](https://apxml.com/models/qwen35-35b-a3b)  
> 4. How Much VRAM Do you need to run a 32B with 32k context? - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1j5kdcm/how\_much\_vram\_do\_you\_need\_to\_run\_a\_32b\_with\_32k/](https://www.reddit.com/r/LocalLLaMA/comments/1j5kdcm/how_much_vram_do_you_need_to_run_a_32b_with_32k/)  
> 5. Quantized Local LLMs: 4-bit vs 8-bit Performance Analysis | SitePoint, accessed on March 25, 2026, [https://www.sitepoint.com/quantized-local-llms-4bit-vs-8bit-analysis/](https://www.sitepoint.com/quantized-local-llms-4bit-vs-8bit-analysis/)  
> 6. ComfyUI Streamlines Local AI Video Generation | NVIDIA Blog, accessed on March 25, 2026, [https://blogs.nvidia.com/blog/rtx-ai-garage-flux-ltx-video-comfyui-gdc/](https://blogs.nvidia.com/blog/rtx-ai-garage-flux-ltx-video-comfyui-gdc/)  
> 7. Top 10 LLMs with No Restrictions in 2026 - Apidog, accessed on March 25, 2026, [https://apidog.com/blog/llms-no-restrictions/](https://apidog.com/blog/llms-no-restrictions/)  
> 8. mradermacher/llama4-dolphin-8B-GGUF - Hugging Face, accessed on March 25, 2026, [https://huggingface.co/mradermacher/llama4-dolphin-8B-GGUF](https://huggingface.co/mradermacher/llama4-dolphin-8B-GGUF)  
> 9. Axolotl vs Unsloth vs TorchTune: Best LLM Fine-Tuning Frameworks in 2026 | Spheron Blog, accessed on March 25, 2026, [https://www.spheron.network/blog/axolotl-vs-unsloth-vs-torchtune/](https://www.spheron.network/blog/axolotl-vs-unsloth-vs-torchtune/)  
> 10. GitHub - hiyouga/LlamaFactory: Unified Efficient Fine-Tuning of 100+ LLMs & VLMs (ACL 2024), accessed on March 25, 2026, [https://github.com/hiyouga/LlamaFactory](https://github.com/hiyouga/LlamaFactory)  
> 11. Qwen3.5 Fine-tuning Guide | Unsloth Documentation, accessed on March 25, 2026, [https://unsloth.ai/docs/models/qwen3.5/fine-tune](https://unsloth.ai/docs/models/qwen3.5/fine-tune)  
> 12. Qwen3.5 - How to Run Locally Guide | Unsloth Documentation, accessed on March 25, 2026, [https://unsloth.ai/docs/models/qwen3.5](https://unsloth.ai/docs/models/qwen3.5)  
> 13. Introducing Gemma 3: The most capable model you can run on a single GPU or TPU, accessed on March 25, 2026, [https://blog.google/innovation-and-ai/technology/developers-tools/gemma-3/](https://blog.google/innovation-and-ai/technology/developers-tools/gemma-3/)  
> 14. Gemma 3 Technical Report - arXiv, accessed on March 25, 2026, [https://arxiv.org/html/2503.19786v1](https://arxiv.org/html/2503.19786v1)  
> 15. Helpful VRAM requirement table for qlora, lora, and full finetuning. : r/LocalLLaMA - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/18o5u0k/helpful\_vram\_requirement\_table\_for\_qlora\_lora\_and/](https://www.reddit.com/r/LocalLLaMA/comments/18o5u0k/helpful_vram_requirement_table_for_qlora_lora_and/)  
> 16. Best Open Source LLMs: Complete 2026 Guide | Contabo Blog, accessed on March 25, 2026, [https://contabo.com/blog/open-source-llms/](https://contabo.com/blog/open-source-llms/)  
> 17. Llama4 - Hugging Face, accessed on March 25, 2026, [https://huggingface.co/docs/transformers/model\_doc/llama4](https://huggingface.co/docs/transformers/model_doc/llama4)  
> 18. Welcome Llama 4 Maverick & Scout on Hugging Face, accessed on March 25, 2026, [https://huggingface.co/blog/llama4-release](https://huggingface.co/blog/llama4-release)  
> 19. Local AI Hardware Guide 2026: PC vs Mac for Running Large AI Models - Kuware.com, accessed on March 25, 2026, [https://kuware.com/blog/local-ai-hardware-guide-2026-pc-vs-mac/](https://kuware.com/blog/local-ai-hardware-guide-2026-pc-vs-mac/)  
> 20. Best LLM Leaderboard 2026 | AI Model Rankings, Benchmarks ..., accessed on March 25, 2026, [https://onyx.app/llm-leaderboard](https://onyx.app/llm-leaderboard)  
> 21. Top 10 Open-source Reasoning Models in 2026 - Clarifai, accessed on March 25, 2026, [https://www.clarifai.com/blog/top-10-open-source-reasoning-models-in-2026](https://www.clarifai.com/blog/top-10-open-source-reasoning-models-in-2026)  
> 22. Models | OpenRouter, accessed on March 25, 2026, [https://openrouter.ai/models](https://openrouter.ai/models)  
> 23. GPU System Requirements for Running DeepSeek-R1 - ApX Machine Learning, accessed on March 25, 2026, [https://apxml.com/posts/gpu-requirements-deepseek-r1](https://apxml.com/posts/gpu-requirements-deepseek-r1)  
> 24. Phi-4: Best Small Language Model for Lightweight AI? - HorizonIQ, accessed on March 25, 2026, [https://www.horizoniq.com/blog/phi-4/](https://www.horizoniq.com/blog/phi-4/)  
> 25. microsoft/phi-4 - Hugging Face, accessed on March 25, 2026, [https://huggingface.co/microsoft/phi-4](https://huggingface.co/microsoft/phi-4)  
> 26. Microsoft's Phi-4 Reasoning Models: A Breakthrough in Small Language Model Performance - Ashish Chadha, accessed on March 25, 2026, [https://ashishchadha11944.medium.com/microsofts-phi-4-reasoning-models-a-breakthrough-in-small-language-model-performance-267a7cfad9c2](https://ashishchadha11944.medium.com/microsofts-phi-4-reasoning-models-a-breakthrough-in-small-language-model-performance-267a7cfad9c2)  
> 27. How to Increase Context Length in LM Studio - LocalLLM.in, accessed on March 25, 2026, [https://localllm.in/blog/lm-studio-increase-context-length](https://localllm.in/blog/lm-studio-increase-context-length)  
> 28. Qwen/Qwen3.5-27B - Hugging Face, accessed on March 25, 2026, [https://huggingface.co/Qwen/Qwen3.5-27B](https://huggingface.co/Qwen/Qwen3.5-27B)  
> 29. GPU System Requirement Guide for Qwen 3.5 - ApX Machine Learning, accessed on March 25, 2026, [https://apxml.com/posts/qwen-3-5-system-requirement-vram-guide](https://apxml.com/posts/qwen-3-5-system-requirement-vram-guide)  
> 30. The 2026 Local LLM Hardware Guide: Surviving the RAM Crisis | by James Rien | Medium, accessed on March 25, 2026, [https://medium.com/@jameshugo598/the-2026-local-llm-hardware-guide-surviving-the-ram-crisis-fa67e8c95804](https://medium.com/@jameshugo598/the-2026-local-llm-hardware-guide-surviving-the-ram-crisis-fa67e8c95804)  
> 31. Best Local LLM Models 2026 | Developer Comparison - SitePoint, accessed on March 25, 2026, [https://www.sitepoint.com/best-local-llm-models-2026/](https://www.sitepoint.com/best-local-llm-models-2026/)  
> 32. Qwen3.5 27B vs 35B: In-Depth Local Performance Comparison - Sonusahani.com, accessed on March 25, 2026, [https://sonusahani.com/blogs/qwen-27b-vs-qwen-35b](https://sonusahani.com/blogs/qwen-27b-vs-qwen-35b)  
> 33. Qwen3.5-35B-A3B quantization quality + speed benchmarks on RTX 5080 16GB (Q8\_0 vs Q4\_K\_M vs UD-Q4\_K\_XL) : r/LocalLLaMA - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1rei65v/qwen3535ba3b\_quantization\_quality\_speed/](https://www.reddit.com/r/LocalLLaMA/comments/1rei65v/qwen3535ba3b_quantization_quality_speed/)  
> 34. Welcome Gemma 3: Google's all new multimodal, multilingual, long context open LLM, accessed on March 25, 2026, [https://huggingface.co/blog/gemma3](https://huggingface.co/blog/gemma3)  
> 35. llama.cpp VRAM Requirements: Complete 2026 Guide to GPU Memory for Local LLMs, accessed on March 25, 2026, [https://localllm.in/blog/llamacpp-vram-requirements-for-local-llms](https://localllm.in/blog/llamacpp-vram-requirements-for-local-llms)  
> 36. Best Self-Hosted LLM Leaderboard 2026 | Open-Weight Model Rankings for Enterprise, accessed on March 25, 2026, [https://onyx.app/self-hosted-llm-leaderboard](https://onyx.app/self-hosted-llm-leaderboard)  
> 37. The Best Open-Source LLMs in 2026 - BentoML, accessed on March 25, 2026, [https://www.bentoml.com/blog/navigating-the-world-of-open-source-large-language-models](https://www.bentoml.com/blog/navigating-the-world-of-open-source-large-language-models)  
> 38. LLM Leaderboard - Vellum AI, accessed on March 25, 2026, [https://vellum.ai/llm-leaderboard](https://vellum.ai/llm-leaderboard)  
> 39. I made a list of every AI benchmark that still has signal in 2025-2026 (and the ones that are completely dead) - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/LocalLLaMA/comments/1rovfbw/i\_made\_a\_list\_of\_every\_ai\_benchmark\_that\_still/](https://www.reddit.com/r/LocalLLaMA/comments/1rovfbw/i_made_a_list_of_every_ai_benchmark_that_still/)  
> 40. GLM-5: From Vibe Coding to Agentic Engineering - Z.ai, accessed on March 25, 2026, [https://z.ai/blog/glm-5](https://z.ai/blog/glm-5)  
> 41. Best LLMs for Coding in 2026: Top 15 Models Compared by Benchmarks, accessed on March 25, 2026, [https://www.edenai.co/post/best-llms-for-coding](https://www.edenai.co/post/best-llms-for-coding)  
> 42. SWE-bench Leaderboards, accessed on March 25, 2026, [https://www.swebench.com/](https://www.swebench.com/)  
> 43. Which Local LLM is Better? A Deep Dive into Open-Source AI Models in 2026 (Benchmarked) | by Likhit Kumar - Medium, accessed on March 25, 2026, [https://medium.com/@likhitkumarvp/which-local-llm-is-better-a-deep-dive-into-open-source-ai-models-in-2026-benchmarked-b786d6e13384](https://medium.com/@likhitkumarvp/which-local-llm-is-better-a-deep-dive-into-open-source-ai-models-in-2026-benchmarked-b786d6e13384)  
> 44. Small Local AI Takes the SQL Test | by John Baima | Feb, 2026 - Medium, accessed on March 25, 2026, [https://medium.com/@jkbaima/small-local-ai-takes-the-sql-test-e04ece24f1f2](https://medium.com/@jkbaima/small-local-ai-takes-the-sql-test-e04ece24f1f2)  
> 45. MedGemma: Our most capable open models for health AI development - Google Research, accessed on March 25, 2026, [https://research.google/blog/medgemma-our-most-capable-open-models-for-health-ai-development/](https://research.google/blog/medgemma-our-most-capable-open-models-for-health-ai-development/)  
> 46. Best Open Source Reasoning Models in 2026: Top Picks for ..., accessed on March 25, 2026, [https://okara.ai/blog/best-open-source-reasoning-models](https://okara.ai/blog/best-open-source-reasoning-models)  
> 47. How to Run FLUX Locally (March 2026): ComfyUI Setup, VRAM ..., accessed on March 25, 2026, [https://localaimaster.com/blog/flux-local-image-generation](https://localaimaster.com/blog/flux-local-image-generation)  
> 48. FLUX.2 Image Generation Models Now Released, Optimized for NVIDIA RTX GPUs, accessed on March 25, 2026, [https://blogs.nvidia.com/blog/rtx-ai-garage-flux-2-comfyui/](https://blogs.nvidia.com/blog/rtx-ai-garage-flux-2-comfyui/)  
> 49. deepbeepmeep/Wan2GP: A fast AI Video Generator for the ... - GitHub, accessed on March 25, 2026, [https://github.com/deepbeepmeep/Wan2GP](https://github.com/deepbeepmeep/Wan2GP)  
> 50. Best Ai Video Generation model you can run on your PC in 2026, accessed on March 25, 2026, [https://www.proxpc.com/blogs/best-ai-video-generation-model-you-can-run-on-your-pc-in-2026](https://www.proxpc.com/blogs/best-ai-video-generation-model-you-can-run-on-your-pc-in-2026)  
> 51. Best Open Source Video Generation Models in 2026: Make the Right Choice - Okara AI, accessed on March 25, 2026, [https://okara.ai/blog/best-open-source-video-generation-models](https://okara.ai/blog/best-open-source-video-generation-models)  
> 52. Wan2.2 : AI Video Generation in Budget GPU | by Mehul Gupta | Data Science in Your Pocket | Medium, accessed on March 25, 2026, [https://medium.com/data-science-in-your-pocket/wan2-2-ai-video-generation-in-budget-gpu-a314238b2a54](https://medium.com/data-science-in-your-pocket/wan2-2-ai-video-generation-in-budget-gpu-a314238b2a54)  
> 53. Wan2.1 GP: generate a 8s WAN 480P video (14B model non quantized) with only 12 GB of VRAM : r/StableDiffusion - Reddit, accessed on March 25, 2026, [https://www.reddit.com/r/StableDiffusion/comments/1j1x611/wan21\_gp\_generate\_a\_8s\_wan\_480p\_video\_14b\_model/](https://www.reddit.com/r/StableDiffusion/comments/1j1x611/wan21_gp_generate_a_8s_wan_480p_video_14b_model/)
