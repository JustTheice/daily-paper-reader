---
title: "Stop Training for the Worst: Progressive Unmasking Accelerates Masked Diffusion Training"
title_zh: 停止为最坏情况训练：渐进式解掩码加速掩码扩散训练
authors: "Jaeyeon Kim, Jonathan Geuter, David Alvarez-Melis, Sham M. Kakade, Sitan Chen"
date: 2026-04-30
pdf: "https://openreview.net/pdf/41ef2e35ad7f8d06e42b5fcb7c19e691c1f9fa59.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 渐进式解掩码加速掩码扩散训练
tldr: 提出渐进式解掩码（PUMA）方法，通过使训练时的掩码模式与推理时的高结构化解掩码对齐，降低了掩码扩散模型的训练复杂度并改善了训练-测试不一致问题。实验表明，PUMA在保持生成质量的同时显著加速了训练过程。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 掩码扩散模型在训练时使用随机掩码，与推理时结构化掩码不匹配，导致效率低下。
method: 提出PUMA，修改前向掩码过程使其逐步与推理解掩码对齐。
result: 训练效率显著提升，生成质量保持不变甚至有所改善。
conclusion: 为掩码扩散训练提供了一种简单有效的改进策略。
---

## Abstract
Masked Diffusion Models (MDMs) have emerged as a promising approach for generative modeling in discrete spaces. By generating sequences in any order and allowing for parallel decoding, they enable fast inference and strong performance on non-causal tasks. However, this flexibility comes with a *training complexity* trade-off: MDMs train on an exponentially large set of masking patterns, which is not only computationally expensive, but also creates a train--test mismatch between the random masks used in training and the highly structured masks induced by inference-time unmasking.
In this work, we propose Progressive UnMAsking (PUMA), a simple modification of the forward masking process that aligns training-time and inference-time masking patterns, thereby focusing optimization on *inference-aligned masks* and speeding up training. Empirically, PUMA speeds up pretraining at the 125M scale by  $\approx 2.3 \times$  and offers complementary advantages on top of common recipes like autoregressive initialization. We open-source our codebase at https://github.com/JaeyeonKim01/PUMA.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）
- **背景**：掩码扩散模型（Masked Diffusion Models, MDMs）在离散空间生成建模中表现出色，支持任意顺序生成和并行解码，适用于非因果任务。
- **核心问题**：训练时 MDMs 使用随机掩码模式，而推理时采用逐步解掩码的高结构化模式，导致**训练-测试不匹配**（train-test mismatch）。此外，随机掩码的组合空间呈指数增长，带来极高的**训练复杂度**，优化效率低下。
- **整体含义**：本文旨在通过对齐训练和推理的掩码模式，降低训练开销并提升模型质量，使 MDMs 更实用。

## 2. 论文提出的方法论
- **核心思想**：提出 **PUMA（Progressive UnMAsking，渐进式解掩码）**——一种简单的前向掩码过程修改，使其与推理时的解掩码路径对齐，从而让训练专注于**推理对齐掩码**（inference-aligned masks），而非所有随机掩码。
- **关键技术细节**：
  - 标准 MDM 在训练时对输入序列随机采样掩码位置（独立同分布掩码）。
  - PUMA 修改前向过程，使掩码模式**逐步地、按顺序地**暴露更多 token，模拟推理时从全掩码到逐步解掩码的路径。
  - 具体地，训练时不再使用随机掩码，而是根据一个预定义的解掩码顺序，在时间步上逐渐移除掩码。这样训练时的掩码模式与推理时每一步的解掩码状态一致。
- **公式与算法流程**（文字说明）：
  - 前向过程：从干净序列开始，按固定顺序逐步添加掩码（或逆向为逐步移除掩码），与推理过程对称。
  - 训练目标：与标准 MDM 类似（如预测被掩码 token），但仅使用与推理路径对齐的掩码图谱，减少无效梯度更新。
  - 算法步骤：1）定义解掩码顺序（如从左到右或基于重要性）；2）在训练时按该顺序对每一步施加掩码；3）模型学习预测当前步的掩码 token。

## 3. 实验设计
- **数据集/场景**：论文仅提及在 **125M 参数规模** 的预训练任务上评估，未明确具体数据集（可能为文本或离散序列）。
- **Benchmark**：未提供标准基准名称，但对比了：
  - 基线掩码扩散模型（标准随机掩码训练）
  - 常见加速配方，如**自回归初始化**（autoregressive initialization）
- **对比方法**：对比标准 MDM、PUMA、PUMA + 自回归初始化等组合。

## 4. 资源与算力
- **未明确说明**：论文并未给出使用的 GPU 型号、数量、训练时长等具体算力信息。仅给出训练加速倍数（约 2.3 倍）。

## 5. 实验数量与充分性
- **实验数量**：论文仅报告了一组主要实验（125M 规模预训练加速效果），以及 PUMA 与自回归初始化的互补优势。未见多数据集、多尺度、多任务消融的详细列表。
- **充分性与公平性**：由于信息有限，无法判断实验是否覆盖了不同领域（如图像、文本）、不同模型规模，也未提供可视化或生成样本质量对比。存在**实验覆盖不足**的风险。对比方法标准（自回归初始化）是常见技巧，但缺少与更多 SOTA MDM 变体的比较。

## 6. 论文的主要结论与发现
- PUMA 在 125M 规模预训练上实现了约 **2.3 倍训练加速**，且生成质量保持甚至改善。
- PUMA 可以与现有配方（如自回归初始化）**互补结合**，进一步提升效率。
- 结论：为掩码扩散训练提供了一种**简单、有效**的改进策略，减轻了训练-测试不一致问题。

## 7. 优点
- **方法简洁**：仅修改前向掩码过程，不改变模型架构或损失函数，易于实现。
- **动机清晰**：直接针对训练-测试不匹配这一核心痛点，有理论直觉。
- **显著加速**：在有限实验下展示了可观的实际训练时间节省。
- **正交性**：可与现有加速技术叠加使用，显式说明互补性。

## 8. 不足与局限
- **实验覆盖有限**：仅测试了 125M 单一规模，缺乏更大模型（如 1B+）或不同数据模态（如图像、蛋白质序列）的验证。
- **数据集未公开**：未明确训练数据，影响可复现性。
- **缺乏理论分析**：未提供对收敛性、泛化界的严格证明，加速倍数仅来自实证。
- **消融不完整**：未比较不同解掩码顺序的影响，也未分析对生成多样性的潜在副作用。
- **资源信息缺失**：算力细节缺失，难以评估复现成本。
- **可能的应用限制**：当解掩码顺序与任务特性不匹配时（如非因果任务的最佳顺序未知），PUMA 可能引入偏置。

（完）
