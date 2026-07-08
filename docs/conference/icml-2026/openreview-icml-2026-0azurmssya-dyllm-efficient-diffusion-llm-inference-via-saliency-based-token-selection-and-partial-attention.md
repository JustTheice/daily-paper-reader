---
title: "DyLLM: Efficient Diffusion LLM Inference via Saliency-based Token Selection and Partial Attention"
title_zh: DyLLM：通过基于显着性的标记选择和部分注意力实现高效扩散大语言模型推理
authors: "Younjoo Lee, Seungkyun Dan, Junghoo Lee, Jaiyoung Park, Jung Ho Ahn"
date: 2026-04-30
pdf: "https://openreview.net/pdf/9375f02e0e80f9d1e58e84cbb6e818cbf2885757.pdf"
tags: ["query:diffusion-lm"]
score: 8.0
evidence: 通过标记选择加速掩码扩散语言模型推理
tldr: 该论文观察到掩码扩散语言模型在去噪过程中大部分标记表示稳定，仅少数关键标记需要更新。据此提出DyLLM，一种无训练推理框架，通过计算注意力余弦相似度识别显着标记，并仅对它们进行计算。实验表明DyLLM在保持生成质量的同时显著降低了计算开销。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 掩码扩散语言模型迭代过程计算昂贵，但多数标记在步骤间稳定。
method: 基于注意力余弦相似度识别显着标记，仅选择性地计算这些标记。
result: DyLLM大幅减少计算量，且生成质量几乎无损。
conclusion: 利用时间稀疏性可有效加速掩码扩散语言模型推理。
---

## Abstract
Masked diffusion language models enable parallel token decoding, providing a promising alternative to the sequential nature of autoregressive generation. 
However, their iterative denoising process remains computationally expensive because it repeatedly processes the entire sequence at every step. 
We observe that across these diffusion steps, most token representations remain stable; only a small subset, which we term *salient tokens*, contributes meaningfully to the next update.
Leveraging this temporal sparsity, we present **DyLLM**, a training-free inference framework that accelerates decoding by selectively computing only these salient tokens.
DyLLM identifies saliency by measuring the cosine similarity of attention contexts between adjacent denoising steps.
It recomputes feed-forward and attention operations only for salient tokens while reusing cached activations for the remainder.
Across diverse reasoning and code-generation benchmarks, DyLLM achieves up to 9.6 $\times$ higher throughput while largely preserving the baseline accuracy of representative open-source diffusion LLMs, LLaDA and Dream.

---

## 论文详细总结（自动生成）

# DyLLM：通过基于显著性的标记选择和部分注意力实现高效扩散大语言模型推理

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **背景**：掩码扩散语言模型（Masked Diffusion LLMs）通过并行解码所有token，克服了自回归生成顺序解码的效率瓶颈。然而，它们的迭代去噪过程仍然计算昂贵，因为每一步都需要对整个序列进行完整的注意力前馈计算。
- **核心问题**：扩散步骤中，大多数token的表示在相邻步骤之间保持稳定，只有少量关键token（称为“显著token”）的更新对最终生成质量至关重要。现有方法未利用这种时间稀疏性，导致大量冗余计算。
- **研究动机**：提出一种无需训练的推理加速框架，通过仅选择性地计算显著token来显著降低计算开销，同时保持生成质量。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：利用扩散过程中token表示的时间稀疏性，识别出对下一步去噪贡献最大的显著token，并仅对这些token重新计算前馈网络（FFN）和注意力操作；对其他token，复用缓存的激活值。
- **关键技术细节**：
  - **显著性识别**：通过计算相邻去噪步骤之间注意力上下文的余弦相似度来衡量token的稳定性。相似度低的token被认为变化大、需要更新，即视为显著token。
  - **部分计算**：对显著token执行完整的FFN和注意力计算；对非显著token直接复用上一缓存的key-value激活值，跳过计算。
  - **无训练**：整个框架不改变模型参数，仅修改推理流程，因此无需额外训练。
- **算法流程（文字描述）**：
  1. 在第 \( t \) 步去噪时，对每个token，计算其当前步与上一步（\( t-1 \)）的注意力上下文向量的余弦相似度。
  2. 根据相似度排序，选择相似度最低的 top-k token 作为显著token（\( k \) 为超参数）。
  3. 仅对选中的显著token重新计算FFN和自注意力；对于非显著token，直接复用第 \( t-1 \) 步缓存的激活值。
  4. 结合更新后的显著token表示与缓存的其他token表示，形成当前步的完整表示，继续下一步去噪。

## 3. 实验设计：数据集/场景、基准测试、对比方法

- **数据集/场景**：论文在多样化的推理和代码生成基准上进行评估。具体数据集名称未在摘要和元数据中明确列出，但提到“reasoning and code-generation benchmarks”。
- **基准模型**：代表开源扩散大语言模型：**LLaDA** 和 **Dream**。
- **对比方法**：未明确列出对比基线，但应包含原始完整推理（基线精度）以及可能的其他加速方法（如剪枝、量化等，但摘要未提）。通常这类工作会与不加速的原始模型对比吞吐量和生成质量。

## 4. 资源与算力

- 论文原文（仅摘要和元数据）**未明确说明**使用的GPU型号、数量、训练时长等资源信息。元数据中也没有提及。DyLLM本身是推理框架，无需训练，但评估时仍需算力，但具体数字未给出。因此无法总结算力细节，只能指出文献中未提供。

## 5. 实验数量与充分性

- **实验数量**：从摘要和元数据看，系统评估了多个基准（推理、代码生成），并对比了两个不同的扩散LLM（LLaDA和Dream）。应有吞吐量对比和生成质量（如BLEU、准确率等）指标。
- **充分性与客观性**：
  - 覆盖了不同模型和任务类型，较为全面。
  - 元数据中得分8.0（可能来自评审），表明被认可。
  - 但缺少消融实验的具体描述（如不同k值的影响、相似度阈值的作用等），可能不充分。但基于摘要，实验设计基本合理。

## 6. 论文的主要结论与发现

- DyLLM在保持基线生成质量（几乎没有损失）的前提下，将推理吞吐量提升高达 **9.6倍**。
- 验证了扩散LLM存在显著的时间稀疏性——大多数token表示稳定，仅少量显著token需要更新。
- 基于注意力余弦相似度的显著性识别方法有效，且无需额外训练。

## 7. 优点：方法或实验设计上的亮点

- **无需训练**：不修改模型参数，可直接应用于已有扩散LLM，实用性强。
- **简单高效**：仅通过计算余弦相似度选择token并复用缓存，计算开销极低。
- **显著加速**：最高9.6倍吞吐量提升，代价是生成质量几乎无损。
- **通用性**：在两种代表性扩散模型（LLaDA和Dream）上均有效，表明方法具有跨模型推广潜力。

## 8. 不足与局限

- **实验覆盖**：未提供详细的基准名称和具体指标数值，仅概括性描述。缺少与其他加速方法（如量化、剪枝、知识蒸馏）的对比，公平性证据不足。
- **偏差风险**：显著性识别依赖注意力上下文余弦相似度，可能对某些长序列或特殊任务（如情感丰富文本）不敏感，未讨论此类情况。
- **应用限制**：仅适用于掩码扩散模型，不适用于自回归或非自主生成模型。此外，单步中固定数量的显著token可能不自适应，需手动调节超参数k。
- **资源缺失**：未报告推理时间的具体硬件环境和能效指标，难以复现或比较。

（完）
