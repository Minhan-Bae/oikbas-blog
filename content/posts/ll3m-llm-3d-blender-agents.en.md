---
title: "LLMs Write Blender Code — LL3M: A Paradigm Shift in Multi-Agent 3D Modeling"
date: 2026-03-28
draft: false
author: MinHanr
tags: ["3d-generation", "llm", "multi-agent", "blender", "paper-review"]
categories: ["Research"]
summary: "LL3M generates 3D assets by having an LLM agent team write Blender Python code — without any 3D data training. We analyze this new paradigm of code-based 3D generation."
description: "LL3M generates editable 3D assets from text via a Plan-Retrieve-Write-Debug-Refine multi-agent pipeline. We analyze its core technologies including BlenderRAG, visual self-critique, and the Co-Creative Loop."
keywords: ["LL3M", "3D generation", "LLM", "Blender", "multi-agent", "code generation", "BlenderRAG", "procedural modeling"]
---

Every existing approach to 3D asset generation **learns from 3D data**. DreamFusion uses Score Distillation from 2D images, Point-E trains on point cloud datasets, and 3DGS works from multi-view images. But what if 3D data weren't needed at all?

**LL3M (Large Language 3D Modelers)** redefines 3D generation as a **code-writing task**. An LLM agent team writes Blender Python code, and that code itself becomes the 3D asset.

## Why Code-Based 3D Generation?

Limitations of existing approaches:

| Problem | Description |
|---------|-------------|
| **Data Scarcity** | Collecting high-quality 3D data is expensive and volumes are insufficient |
| **Non-Editable** | Implicit representations like NeRF and 3DGS are difficult for artists to edit directly |
| **Workflow Disconnect** | Generated outputs are incompatible with existing DCC tools (Blender, Maya, Houdini) |

LL3M's approach: LLMs are already good at writing code. Blender has a comprehensive Python API. **If an LLM writes Blender code, you get a 3D asset.**

Characteristics of the output:
- **Human-readable** Python code
- **Editable**: Changing parameters alters the geometry
- **Modular**: Independent code blocks per object
- **Blender-native**: Artists can use results immediately

## Multi-Agent Architecture

The core of LL3M is a 5-stage agent chain:

**1. Planner** — Plans the overall structure. A request for "medieval castle" is decomposed into "towers, walls, gate, moat."

**2. Retriever** — Searches BlenderRAG for the necessary API functions and examples. Finds "how to create a cylinder with B-mesh" and passes it to the Writer.

**3. Writer** — Writes the actual Blender Python code. Leverages advanced Blender features including B-meshes, Geometry Modifiers, and Shader Nodes.

**4. Debugger** — Executes the code and fixes errors. Automatically resolves syntax errors, runtime errors, and API compatibility issues.

**5. Refiner** — Evaluates the rendered results through visual self-critique and improves quality.

### BlenderRAG: The Core Knowledge Base

The entire Blender API documentation is built as a RAG (Retrieval-Augmented Generation) system:
- Function signatures + type information
- Actual usage example code
- Enables agents to **call the correct Blender functions**

Without this, LLMs hallucinate non-existent APIs. BlenderRAG is the key to code accuracy.

### Co-Creative Loop

After the system generates automatically, the user provides additional editing instructions:
```
User: "Make me a chair"
LL3M: [Generates chair]
User: "Make the legs thinner"
LL3M: [Modifies leg radius parameter in code → regenerates]
User: "Make it steampunk style"
LL3M: [Swaps shader nodes → applies metallic texture]
```

## Comparison with Existing 3D Generation Methods

| Method | Input | Output | Editability | 3D Data Required |
|--------|-------|--------|-------------|------------------|
| DreamFusion | Text | NeRF | Difficult | Indirect (2D) |
| Point-E / Shap-E | Text | Mesh | Limited | Yes |
| 3DGS | Images | Gaussian | Difficult | Images |
| **LL3M** | **Text** | **Blender code** | **Full** | **None** |

## DCC Tool Extensibility

What LL3M has proven is the viability of the pattern "LLM + RAG + multi-agent → DCC tool automation." This pattern is not limited to Blender:

- **Blender** (LL3M) → Python API + BlenderRAG
- **Houdini** → VEX/Python API + HoudiniRAG
- **Maya** → MEL/Python API + MayaRAG
- **Unreal Engine** → Blueprint/C++ + UnrealRAG

By building a RAG from the target DCC tool's API documentation, the same multi-agent architecture can be reused.

## Limitations

- **Speed**: Sequential execution of the multi-agent chain takes several minutes per generation
- **Organic Shape Limitations**: Code-based approaches struggle with complex organic forms like human bodies and animals
- **Benchmarks**: No quantitative comparison metrics in the paper (qualitative comparisons only)
- **DCC Lock-In**: Blender-specific — other tools require separate RAG systems

## Implications

LL3M represents a paradigm shift from "AI generates 3D" to "AI generates 3D **code**." This shift matters because code is something humans can understand, modify, and reuse. It presents a future where collaboration between AI and artists takes place on the common language of **code**, rather than through implicit representations.

---

*This post was written based on research notes from the personal knowledge system vault.*

Sources:
- [arXiv: 2508.08228](https://arxiv.org/abs/2508.08228)
- [Project Page](https://threedle.github.io/ll3m)
- [GitHub](https://github.com/threedle/ll3m)
