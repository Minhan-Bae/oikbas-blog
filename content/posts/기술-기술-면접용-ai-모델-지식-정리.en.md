---
author: MinHanr
categories:
- General
cover:
  alt: person writing on white paper
  caption: Photo by [UX Indonesia](https://unsplash.com/photos/person-writing-on-white-paper-qC2n6RQU4Vw)
    on [Unsplash](https://unsplash.com)
  image: https://images.unsplash.com/photo-1587440871875-191322ee64b0?crop=entropy&cs=tinysrgb&fit=max&fm=jpg&ixid=M3w5MDgzMTR8MHwxfHNlYXJjaHwzfHxSZXNlYXJjaHxlbnwwfDB8fHwxNzc0NjgyNDE1fDA&ixlib=rb-4.1.0&q=80&w=1080
date: '2025-12-15'
draft: false
slug: 기술-기술-면접용-ai-모델-지식-정리
tags:
- Research
- domain/llm
- domain/diffusion
title: 1. Transformer Explained — AI Model Knowledge for Technical Interviews
---

## 1. Transformer Explained

```
【Transformer Core Concepts】

■ Background
- Proposed in Google's 2017 paper "Attention is All You Need"
- Designed to overcome the sequential processing limitations of traditional RNN/LSTM
- Parallel processing enables dramatically faster training

■ Core Architecture
1. Self-Attention Mechanism
   - Simultaneously computes relationships between all positions in the input sequence
   - Transforms inputs into three vectors: Query, Key, and Value to compute similarity
   - Each word captures its relevance to every other word in the sentence

2. Multi-Head Attention
   - Runs multiple Attention operations in parallel
   - Captures contextual information from diverse perspectives
   - Typically uses 8 or 12 heads

3. Positional Encoding
   - Since there is no sequential processing, positional information is injected separately
   - Uses fixed patterns with Sin/Cos functions or learnable embeddings

4. Encoder-Decoder Structure
   - Encoder: Transforms input into high-dimensional representations (6-layer stack)
   - Decoder: Generates output based on encoded information
   - Each layer consists of Self-Attention + Feed Forward

■ Key Features and Advantages
- Resolves the long-range dependency problem
- Reduced training time through parallel processing
- Enables global context understanding
- Mitigates gradient vanishing through Residual Connections

■ Applications
- NLP: Foundation for models like BERT, GPT, T5
- Vision: Vision Transformer (ViT)
- Multi-modal: CLIP, DALL-E
- Translation, summarization, question answering, and more
```

---

## 2. VAE (Variational AutoEncoder) Explained

```
【VAE Core Concepts】

■ Basic Concept
- A probabilistic variant of the AutoEncoder
- A generative model that compresses data into a latent space and reconstructs it
- Learns the probability distribution of input data to generate new samples

■ Architecture
1. Encoder (Recognition Model)
   - Transforms input data x into distribution parameters of latent variable z
   - Outputs mean (μ) and variance (σ²)
   - Approximates q(z|x) (posterior approximation)

2. Latent Space
   - Constrained to follow a normal distribution N(μ, σ²)
   - Uses the Reparameterization Trick: z = μ + σ * ε (ε~N(0,1))
   - This enables backpropagation

3. Decoder (Generative Model)
   - Reconstructs original data x from latent variable z
   - Models p(x|z)

■ Loss Function
1. Reconstruction Loss
   - Difference between input and reconstructed output
   - Uses MSE or Binary Cross-Entropy

2. KL Divergence
   - Difference between learned distribution q(z|x) and prior p(z)
   - Regularizes the latent space to follow a normal distribution
   - Prevents overfitting and forms a continuous latent space

Total Loss = Reconstruction Loss + β * KL Divergence

■ Features and Advantages
- Unlike standard AutoEncoders, can generate new data through probabilistic sampling
- Latent space is continuous and meaningfully structured
- Interpolation is possible (generating intermediate values between two data points)
- Theoretically sound probabilistic framework

■ Disadvantages and Limitations
- Generation quality may be somewhat lower than GAN (blurry images)
- Requires balancing KL Divergence and Reconstruction
- Posterior Collapse can occur

■ Applications
- Image generation and transformation
- Anomaly Detection
- Data compression and dimensionality reduction
- Drug design, molecular structure generation
```

---

## 3. Diffusion Model Explained

```
【Diffusion Model Core Concepts】

■ Fundamental Principle
- A generative model inspired by the thermodynamic diffusion process
- Forward Process: Gradually adds noise to data
- Reverse Process: Learns to recover the original from noise

■ Forward Diffusion Process
- Starts from original image x₀
- Gradually adds Gaussian noise over T steps
- At each step: q(xₜ|xₜ₋₁) = N(√(1-βₜ)·xₜ₋₁, βₜI)
- Finally, xₜ becomes pure Gaussian noise
- Markov Chain structure where each step depends only on the previous step

■ Reverse Diffusion Process
- Starts from noise xₜ and recovers original x₀
- Neural network learns p(xₜ₋₁|xₜ)
- In practice, trained to predict the noise at each step
- Primarily uses U-Net-based architectures

■ Training Method
- Objective: Train a Denoising Network to predict added noise
- Select a random timestep t and add noise
- Network predicts the added noise ε
- Minimize MSE Loss between actual and predicted noise

Loss = E[||ε - ε_θ(xₜ, t)||²]

■ Key Features
- High-quality image generation (more stable than GAN)
- Stable training with no Mode Collapse
- Generates highly diverse samples
- Gradual generation process enables intermediate-stage control

■ Developments and Variants
1. DDPM (Denoising Diffusion Probabilistic Models)
   - The foundational Diffusion model

2. DDIM (Denoising Diffusion Implicit Models)
   - Improved sampling speed (reduced step count)
   - Enables deterministic sampling

3. Latent Diffusion Models (LDM)
   - Foundation of Stable Diffusion
   - Performs Diffusion in a VAE-compressed latent space
   - Dramatically reduces computational cost

4. Conditional Diffusion
   - Text-to-Image (text conditioning)
   - Enhanced control via Classifier-Free Guidance

■ Disadvantages
- Slow generation speed (requires tens to hundreds of steps)
- Training demands significant computational resources
- Sampling time is longer than GAN

■ Applications
- Text-to-Image: Stable Diffusion, DALL-E 2, Imagen
- Image Editing, Inpainting
- Super Resolution
- Video Generation
- Audio Generation
```

---

## 4. RoLA (Rank-One Low-rank Adaptation) Explained

```
【RoLA Core Concepts】

■ Background and Origin
- A variant of the LoRA technique
- Proposed for even more parameter-efficient fine-tuning
- Reduces memory usage compared to LoRA through Rank-1 constraints

※ Understanding LoRA first is necessary:
  LoRA (Low-Rank Adaptation)
  - An efficient fine-tuning technique for large language models
  - Freezes original weights W and adds low-rank decomposition matrices
  - W + ΔW, where ΔW = BA (B: m×r, A: r×n, r<<min(m,n))
  - Greatly reduces the number of trainable parameters

■ Core Idea of RoLA
- Restricts LoRA's rank r to 1 (Rank-One)
- ΔW = b·aᵀ (b: m×1, a: n×1)
- Represents weight updates with just two vectors
- An extremely compressed form of adaptation

■ Structural Features
1. Parameter Efficiency
   - LoRA (rank r): Trainable parameters = (m+n)×r
   - RoLA (rank 1): Trainable parameters = m+n
   - Significantly lower memory usage

2. Update Method
   - Only two vectors (b, a) are learned per layer
   - Weight update matrix generated via outer product
   - W_new = W_frozen + α·(b·aᵀ)

3. Scaling Factor
   - α is a hyperparameter controlling update strength
   - Typically set independently of the learning rate

■ Advantages
- Extremely low memory usage
- Fast training speed
- Easy model merging
- Supports simultaneous multi-task learning
- No changes to original model architecture

■ Disadvantages and Limitations
- Expressiveness may be more limited than LoRA
- Potential performance degradation on complex tasks
- Reduced learning capacity due to Rank-1 constraint

■ Application Targets
- Transformer Attention weights (Q, K, V, O)
- Feed-Forward layers
- Large Language Model (LLM) fine-tuning
- Vision models such as Stable Diffusion

■ Comparison of LoRA Variants
1. LoRA: The standard approach, uses rank r
2. RoLA: Extreme compression with Rank-1
3. AdaLoRA: Dynamically adjusts rank
4. QLoRA: Combined with Quantization

■ Practical Tips
- RoLA is effective for simple domain adaptation
- LoRA (rank 4-8) is recommended for complex tasks
- Useful in extremely memory-constrained environments
- Enables large-model fine-tuning on a single A100
```

---

## Interview Tips

```
【Structuring Effective Answers】

1. Definition and Background (Why)
   - Why this technology emerged
   - Limitations of previous methods

2. Core Architecture and Principles (What & How)
   - Key components
   - Operating mechanisms

3. Features, Pros, and Cons
   - Technical strengths
   - Limitations and trade-offs

4. Applications and Practical Use
   - Real-world use cases
   - Connect to relevant project experience

【Additional Points Worth Preparing】

- Transformer: Attention Score calculation formula, GPT vs BERT differences
- VAE: β-VAE, Disentanglement concept
- Diffusion: DDPM vs DDIM differences, Classifier-Free Guidance
- RoLA: Quantitative comparison with LoRA, actual memory savings
```

---

Practice explaining each concept within **2-3 minutes** based on this material. The key is to develop your answer logically in the order of **"Why -> What -> How -> Where."**

If you have additional questions or need a deeper explanation of any concept, feel free to ask!
---
## Related Notes
- PathFinder
- TaylorDub
- Memesis
