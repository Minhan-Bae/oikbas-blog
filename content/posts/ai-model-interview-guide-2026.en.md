---
title: "Mastering AI Model Interviews in 2026 — 7 Essential Concepts from Transformer to RAG"
date: 2026-03-28
draft: false
author: MinHanr
tags: ["ai-interview", "transformer", "diffusion", "MoE", "LoRA", "RLHF", "DPO", "RAG"]
categories: ["Tutorial"]
summary: "A structured guide to the 7 essential concepts that invariably appear in AI/ML interviews, organized as 'Why → What → How → Where.' Reflects the latest developments as of March 2026."
description: "Transformer, VAE, Diffusion Model, LoRA/RoLA, MoE, RLHF/DPO, RAG — a comprehensive guide to the 7 must-know AI interview concepts, updated to 2026 standards. Covers the latest trends including Flash Attention 3, Flow Matching, and Agentic RAG."
keywords: ["AI interview", "Transformer", "Diffusion Model", "LoRA", "MoE", "RLHF", "DPO", "RAG", "2026", "interview preparation"]
---

This guide covers the 7 essential concepts that repeatedly appear in AI/ML interviews. Each concept is structured in a **"Why → What → How → Where"** sequence so you can explain it within **2–3 minutes**, and reflects the latest developments as of March 2026.

---

## 1. Transformer

### Background

Proposed in Google's 2017 paper "Attention is All You Need." It was designed to overcome the sequential processing limitations of RNN/LSTM, and its parallel processing capability dramatically improved training speed.

### Core Architecture

**Self-Attention Mechanism**: Simultaneously computes relationships between all positions in the input sequence.

$$\text{Attention}(Q,K,V) = \text{softmax}\left(\frac{QK^T}{\sqrt{d_k}}\right) \cdot V$$

- **Multi-Head Attention**: Runs multiple Attention operations in parallel to capture context from diverse perspectives (8–12 Heads)
- **Positional Encoding**: Since there is no sequential processing, positional information is injected separately. Latest: RoPE (Rotary Position Embedding)
- **Encoder-Decoder**: 6-layer stack, each layer consists of Self-Attention + Feed Forward

### Latest Developments in 2026

| Technology | Key Feature | Effect |
|------------|------------|--------|
| **Flash Attention 3** | H100 optimized, warp specialization | 2–4x speedup over original |
| **GQA** (Grouped-Query Attention) | Query heads grouped to share KV | Balances MHA quality with MQA efficiency |
| **Router-based dynamic attention** | Dynamically selects MHA/GQA/MQA branches per token | Explores optimal quality-efficiency balance through learning |

### Applications

NLP (BERT, GPT, Llama, Qwen), Vision (ViT, DiT), Multi-modal (CLIP, DALL-E, GPT-4V, Gemini)

---

## 2. VAE (Variational AutoEncoder)

### Basic Concept

A probabilistic variant of the AutoEncoder. It is a generative model that compresses data into a Latent Space and reconstructs it, learning the probability distribution of input data to generate new samples.

### Architecture

1. **Encoder**: Input x → outputs mean (μ) and variance (σ²). Approximates q(z|x)
2. **Latent Space**: Reparameterization Trick — z = μ + σ × ε (ε~N(0,1)) enables backpropagation
3. **Decoder**: Reconstructs original data x from latent variable z. Models p(x|z)

### Loss Function

$$\text{Total Loss} = \text{Reconstruction Loss} + \beta \cdot \text{KL Divergence}$$

- Reconstruction Loss: Difference between input and reconstructed output (MSE or BCE)
- KL Divergence: Difference between the learned distribution and prior → ensures regularity of the latent space

### Current Status as of 2026

A core component of Stable Diffusion, responsible for compressing images into Latent Space. VQ-VAE variants are actively used for discrete representation learning.

---

## 3. Diffusion Model

### Basic Principle

A generative model inspired by the thermodynamic diffusion process. It learns by gradually adding noise to data (Forward) and then restoring the original from noise (Reverse).

$$L = E\left[\|\epsilon - \epsilon_\theta(x_t, t)\|^2\right]$$

### Evolution of Key Developments

| Generation | Model | Key Feature |
|------------|-------|-------------|
| 1st Gen | DDPM | Foundational Diffusion |
| 2nd Gen | DDIM | Reduced steps, Deterministic |
| 3rd Gen | **Latent Diffusion (LDM)** | Operates in VAE latent space → Stable Diffusion |
| 4th Gen | **DiT** | U-Net → Transformer backbone |
| 5th Gen | **Flow Matching** | ODE straight-line paths, maximized theoretical efficiency |
| Latest | **Consistency Models** | 1–2 step generation, fundamental speed solution |

### Key Interview Points

- Differences between DiT and U-Net
- Why Flow Matching is more efficient than DDPM (straight-line trajectories → 60% NFE reduction)
- Classifier-Free Guidance principles

---

## 4. LoRA / RoLA (Low-Rank Adaptation)

### Core Idea

Reduces the cost of Full Fine-tuning large models to the 0.1–1% level. Freezes the original weights W and trains only low-rank decomposition matrices.

$$W_{\text{new}} = W_{\text{frozen}} + \alpha \cdot (B \cdot A)$$

B: m×r, A: r×n, r ≪ min(m,n)

### 2026 Variant Comparison

| Technique | Key Feature | Best Suited For |
|-----------|-------------|----------------|
| **LoRA** | Baseline, rank r | General purpose |
| **RoLA** | Rank-1 extreme compression | Memory-constrained |
| **AdaLoRA** | Dynamic rank adjustment | Varying layer importance |
| **QLoRA** | Combined with 4-bit quantization | 70B training on single GPU |
| **DoRA** | Separates direction/magnitude | When near-Full FT performance is needed |

### Practical Tips

- Simple domain adaptation: rank 4–8
- Complex tasks: rank 16–64, consider DoRA
- Multi-user serving: Hot-swap LoRA adapters to serve multiple users from a single base model

---

## 5. MoE (Mixture of Experts)

### Basic Principle

Places multiple "Expert" sub-networks within a single model, with a router activating only the most suitable experts for each input. Total parameters are large, but only a subset is used during inference for efficient scaling.

$$\text{Output} = \sum_{i \in \text{Top-k}} G(x)_i \cdot \text{Expert}_i(x)$$

### Key Model Examples

| Model | Experts | Total Parameters | Active Parameters |
|-------|---------|-----------------|-------------------|
| Mixtral 8x7B | 8, Top-2 | 56B | ~13B |
| DeepSeek-V3 | 256 | 671B | - |
| Grok-1 | 8 | 314B | - |

### Essential Interview Comparison: Dense vs MoE

- Dense 7B ≈ MoE 8x7B(Top-2) inference cost
- However, MoE's effective capacity is much larger
- Trade-off: **Memory** (MoE is larger) vs **Computation** (equivalent) vs **Quality** (MoE wins)

---

## 6. RLHF / DPO (Alignment)

### Why Is It Needed

Pre-trained LLMs only learn "next token prediction." **Alignment to human preferences** is needed to generate helpful, honest, and harmless responses.

### RLHF: 3-Stage Pipeline

1. **SFT** (Supervised Fine-Tuning): High-quality demo data for basic instruction following
2. **Reward Model**: Train reward model from human preference rankings (A > B)
3. **PPO**: RL optimization based on reward model + KL constraint

### DPO: The Mainstream of 2026

Directly optimizes policy from preference data without a separate reward model. Mathematically proven to be equivalent to RLHF.

| Item | RLHF (PPO) | DPO |
|------|-----------|-----|
| Reward Model | Required | **Not required** |
| Training Stability | Low | **High** |
| Implementation Complexity | High | **Low** |
| Theoretical Equivalence | Baseline | Equivalent to RLHF |

### Additional Variants (Bonus Points)

- **ORPO**: Integrates SFT + DPO into a single stage
- **KTO**: Learns from individual "good/bad" labels without preference pairs
- **GRPO** (DeepSeek): Simplifies PPO with group-level relative rewards

---

## 7. RAG (Retrieval-Augmented Generation)

### Why Is It Needed

LLM knowledge is fixed to training data (cutoff problem) and Hallucination occurs. RAG is a cost-efficient solution that retrieves external knowledge and injects it dynamically.

### Architecture: 3 Stages

1. **Indexing**: Documents → chunk splitting → embedding vectors → vector DB storage
2. **Retrieval**: Query vectorization → similarity search → Top-k retrieval
3. **Generation**: Inject search results as context into LLM → response generation

### RAG vs Fine-tuning (Frequently Asked in Interviews)

| Item | RAG | Fine-tuning |
|------|-----|------------|
| Knowledge Update | **Immediate** (swap documents) | Retraining required |
| Cost | **Low** | High |
| Hallucination Reduction | **Effective** | Limited |
| Style Control | Limited | **Effective** |

### 2026: RAG 2.0

| Advancement | Key Feature |
|-------------|-------------|
| **Hybrid Search** | BM25 (keyword) + Vector (semantic) combined |
| **Recursive Retrieval** | Search → Summarize → Re-search cycle |
| **Multimodal RAG** | Images, tables, and graphs as retrieval targets |
| **Agentic RAG** | Agent autonomously determines retrieval strategy, combines with tool calling |

### Practical Optimization Points

- Chunk size: 256–1024 tokens, adjust per task
- Reranking: Cross-encoder for re-ordering search results
- Prompt: "Answer based on the given context"

---

## Interview Answer Structure Tips

You can deliver logical, complete answers by structuring every concept in these 4 stages:

1. **Definition and Background (Why)**: Why this technology emerged, limitations of previous methods
2. **Core Architecture and Principles (What & How)**: Key components, operating mechanisms
3. **Features, Strengths, and Weaknesses**: Technical strengths, trade-offs
4. **Applications and Practical Use**: Real-world use cases, connect to project experience

---

*This article was written based on research notes from the OIKBAS vault. As of March 2026.*
