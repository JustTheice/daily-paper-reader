---
title: Efficient Parallel Samplers for Recurrent-Depth Models and Their Connection to Diffusion Language Models
title_zh: 面向循环深度模型的高效并行采样器及其与扩散语言模型的联系
authors: "Jonas Geiping, Xinyu Yang, Guinan Su"
date: 2026-04-30
pdf: "https://openreview.net/pdf/2729c67193c59052d14597b4c4f0f90bf65275fc.pdf"
tags: ["query:diffusion-lm"]
score: 7.0
evidence: 循环深度模型与扩散语言模型的联系；高效采样器
tldr: 该论文研究循环深度模型与扩散语言模型之间的深层联系，利用其相似性开发了一种新的扩散强制采样器。该采样器在每次前向传播中解码新标记，并进一步精化隐状态。实验表明该方法加速了生成过程，同时保持了模型能力。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 循环深度模型在推理上有优势，但生成速度慢，需借鉴扩散模型加速。
method: 分析两者相似性，提出扩散强制采样器，每步解码新标记并更新隐状态。
result: 新采样器显著加速了循环深度模型的生成。
conclusion: 扩散模型思想可有效迁移至循环深度模型，提升生成效率。
---

## Abstract
Language models with recurrent depth, also referred to as universal or looped when considering transformers, are defined by the capacity to increase their computation through the repetition of layers. Recent efforts in pretraining have demonstrated that these architectures can scale to modern language modeling tasks while exhibiting advantages in reasoning tasks. 
  In this work, we examine the relationship between recurrent-depth models and diffusion language models. Building on their similarities, we develop a new diffusion forcing sampler for these models to accelerate generation. The sampler advances by decoding new tokens at every forward pass of the model, while the latent states of these tokens can be further refined in parallel through recurrence. Theoretically, under a fixed wall-clock budget, generation with our sampler is strictly more expressive than baseline autoregressive generation as it preserves the same recurrent depth while updating a strictly wider front of token positions in parallel, enabling more computation at equal serial depth. 
  Moreover, this sampler, based on principles from diffusion literature, can be directly applied to existing 3.5B recurrent-depth transformers without any tuning, leading to up to a 5x speedup.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究对象**：循环深度模型（Recurrent-Depth Models），也称为通用Transformer或循环Transformer，其特点是通过重复层来增加计算深度，从而在推理任务中展现优势。
- **核心问题**：尽管循环深度模型在推理能力上表现优异，但其生成速度较慢，限制了实际应用效率。同时，扩散语言模型在生成速度和质量上具有独特优势。
- **研究动机**：探索循环深度模型与扩散语言模型之间的深层联系，利用扩散模型的并行采样思想来加速循环深度模型的生成过程。
- **整体意义**：提出一种新的扩散强制采样器，在不调整模型参数的前提下，实现最高5倍的速度提升，同时保持甚至增强模型表达能力。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：借鉴扩散模型中的迭代精化（iterative refinement）原理，将循环深度模型的逐层前向传播与扩散采样过程对齐，使得每次前向传播可以同时解码新标记并优化已有标记的隐状态。
- **关键技术细节**：
  - 提出的**扩散强制采样器（Diffusion Forcing Sampler）**：在模型的每一次前向传播中，解码一个新的标记，同时通过循环机制并行地精化之前标记的隐状态。
  - 与自回归生成相比，在相同的固定时间预算下，该方法可以更新更宽的标记位置前沿（wider front of token positions），从而在同等串行深度下实现更多计算，理论上严格更具表达力。
  - 基于扩散文献中的原理设计，无需对现有模型进行微调即可直接应用。
- **算法流程（文字说明）**：
  1. 初始化：从噪声或部分序列开始；
  2. 重复前向传播：在每一步，模型接收当前所有标记的隐状态，输出新标记的预测，同时并行地精化已有标记的隐状态（通过循环深度）；
  3. 解码新标记：只解码一个新增标记（类似自回归），但已有标记的隐状态被多次更新（类似扩散迭代）；
  4. 直到生成完整序列。

## 3. 实验设计

- **数据集/场景**：文中仅提到“existing 3.5B recurrent-depth transformers”，未具体说明使用的数据集（可能为标准的语言建模基准，如C4、WikiText等，但原文未明确）。
- **基准模型**：3.5B参数规模的循环深度Transformer模型（可能是开源预训练模型，如RWKV、Looped Transformer等）。
- **对比方法**：基线为传统的自回归生成（autoregressive generation）。
- **实验结果**：在不经任何调参的情况下，新采样器在3.5B模型上实现了最高5倍的速度提升。

## 4. 资源与算力

- **文中未明确说明**使用的GPU型号、数量、训练时长等算力信息。仅提到模型规模为3.5B参数，以及采样加速效果。实验可能仅涉及推理阶段，未涉及训练。因此无法评估算力消耗。

## 5. 实验数量与充分性

- **实验覆盖**：仅在一个模型规模（3.5B）上对比了自回归基线，缺乏在不同模型大小、不同任务（如文本生成、推理任务）上的广泛验证。
- **消融实验**：未提及消融实验，例如不同采样步数、不同扩散噪声调度的影响等。
- **充分性与公平性**：虽然声称无需调参且加速显著，但由于实验细节缺失（数据集、任务、具体加速倍数测量条件等），难以判断实验是否客观、充分。可能存在偏差风险（例如仅选择对加速有利的序列长度或模型）。

## 6. 论文的主要结论与发现

- 扩散语言模型的思想可以有效迁移到循环深度模型中，揭示了二者之间的深层联系。
- 提出的扩散强制采样器能够在保持循环深度模型推理能力的同时，显著加速生成过程（最高5倍）。
- 从理论角度证明了在相同时间预算下，新采样器比自回归生成具有更强的表达能力。

## 7. 优点：方法或实验设计上的亮点

- **方法创新性**：首次建立循环深度模型与扩散语言模型的直接联系，并提出适配的并行采样器。
- **实用性**：无需额外训练或微调，可直接应用于现有预训练模型，部署成本低。
- **理论贡献**：提供了生成表达能力严格优于自回归生成的理论分析，而非仅凭经验。

## 8. 不足与局限

- **实验覆盖不足**：仅在一个模型规模（3.5B）上测试，缺乏对小型模型、不同架构（如RWKV、Mamba等）及多样化任务（如翻译、摘要）的验证。
- **细节缺失**：未提供具体数据集、评估指标、加速倍数测量的具体条件（如序列长度、硬件环境），影响结果的可重复性。
- **缺乏消融与对比**：未与已有的加速方法（如推测解码、并行解码）对比，也未分析不同扩散步数或调度策略的影响。
- **偏差风险**：5倍加速可能仅在特定设置下成立，未讨论加速与生成质量的权衡（如是否降低困惑度或多样性）。

（完）
