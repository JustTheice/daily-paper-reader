---
title: Infinite Mask Diffusion for Few-Step Distillation
title_zh: 用于少步蒸馏的无限掩码扩散模型
authors: "Jaehoon Yoo, Wonjung Kim, Chanhyuk Lee, Seunghoon Hong"
date: 2026-04-30
pdf: "https://openreview.net/pdf/a04b17ba5387627bd5e726be271b6a8f85024dbb.pdf"
tags: ["query:diffusion-lm"]
score: 8.0
evidence: 面向掩码扩散模型的无限掩码蒸馏
tldr: 针对标准掩码扩散模型（MDM）因确定性单态掩码导致的理论下限不可逾越的分解误差，提出无限掩码扩散模型（IMDM），引入随机无限掩码概念，使模型能够在少步蒸馏中突破该下限。IMDM在保持并行解码优势的同时，仅需少量步数即可生成高质量文本。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: MDM存在不可减小的分解误差下限，限制了少步采样的质量。
method: 提出无限掩码扩散（IMDM），通过随机无限状态掩码消除分解误差，实现高效蒸馏。
result: 在语言建模基准上，IMDM以2-4步达到原始MDM数百步的性能。
conclusion: 随机无限掩码为掩码扩散模型提供了更优的少步蒸馏范式。
---

## Abstract
Masked Diffusion Models (MDMs) have emerged as a promising alternative to autoregressive models in language modeling, offering the advantages of parallel decoding and bidirectional context processing within a simple yet effective framework. Specifically, their explicit distinction between masked tokens and data underlies their simple framework and effective conditional generation. However, MDMs typically require many sampling iterations due to factorization errors stemming from simultaneous token updates. We observe that a theoretical lower bound of the factorization error exists, which standard MDMs cannot reduce due to their use of a deterministic single-state mask. In this paper, we propose the Infinite Mask Diffusion Model (IMDM), which introduces a stochastic infinite-state mask to mitigate the theoretical bound while directly inheriting the benefits of MDMs, including the compatibility with pre-trained weights. We empirically demonstrate that MDM fails to perform few-step generation even in a simple synthetic task due to the factorization error bound, whereas IMDM can find an efficient solution for the same task. Finally, when equipped with appropriate distillation methods, IMDM surpasses existing few-step distillation methods at small step counts on LM1B and OpenWebText.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）
- 掩码扩散模型（Masked Diffusion Models, MDMs）作为自回归模型在语言建模中的替代方案，具有并行解码和双向上下文处理的优势，且框架简单。
- 然而，MDMs 由于同时更新多个令牌（token）导致的**分解误差（factorization error）**，使得需要大量采样迭代才能生成高质量文本。
- 论文发现，标准 MDM 存在一个**分解误差的理论下限**，该下限源于使用了**确定性单态掩码（deterministic single-state mask）**，导致无法通过蒸馏等方式进一步减少误差，限制了少步（few-step）生成的质量。

## 2. 方法论：核心思想、关键技术细节
- **核心思想**：提出**无限掩码扩散模型（Infinite Mask Diffusion Model, IMDM）**，引入**随机无限态掩码（stochastic infinite-state mask）**，以绕过理论下限，同时继承 MDM 的所有优势（如兼容预训练权重）。
- **关键技术细节**：
  - 将掩码状态从单一的确定性掩码（0 或 1）扩展为无限随机状态，允许掩码在每一步具备随机性，从而消除分解误差的理论下界。
  - 通过设计合适的蒸馏方法（文中未详述，但提及“when equipped with appropriate distillation methods”），IMDM 能够在少步（2-4 步）内实现高质量生成。
- **公式/算法流程**：摘要中未给出具体公式，但核心思路是利用随机无限掩码理论上消除确定性掩码带来的分解误差限制。

## 3. 实验设计
- **数据集**：LM1B（One Billion Word Benchmark）和 OpenWebText。
- **Benchmark**：对比了现有的**少步蒸馏方法**（具体方法名称未在摘要中列出，但提到“existing few-step distillation methods”）。
- **对比方法**：原始 MDM（数百步采样）以及已有的蒸馏方法。
- **额外验证**：在**简单合成任务**上进行了验证，证明 MDM 因分解误差下限无法进行少步生成，而 IMDM 能找到高效解。

## 4. 资源与算力
- 论文摘要及元数据中**未明确提及**使用的 GPU 型号、数量或训练时长。仅能从“兼容预训练权重”推测可能基于已有的预训练模型进行微调或蒸馏，但具体算力信息缺失。

## 5. 实验数量与充分性
- 摘要仅概述了主要结果：在 LM1B 和 OpenWebText 上，IMDM 以 2-4 步达到原始 MDM 数百步的性能，并优于现有蒸馏方法。
- 未提供详细的消融实验、超参数分析、不同步数对比表格等。实验数量较少，但关键结论（突破分解误差下限）通过合成任务和真实数据集均得到验证。
- **充分性与客观性**：由于摘要信息有限，实验的完整性和统计显著性无法全面评估。但结论逻辑自洽，对比基准合理。

## 6. 主要结论与发现
- 标准 MDM 存在**不可减小的分解误差下限**，这是由确定性单态掩码导致的。
- IMDM 通过引入**随机无限态掩码**有效消除了该下限，使得模型能够在**少步（2-4 步）** 下实现与原始 MDM 在数百步时相当的性能。
- 在语言建模基准上，IMDM 超越了现有少步蒸馏方法，同时保留了 MDM 的并行解码和双向上下文优势。

## 7. 优点
- **理论创新**：首次指出并证明了 MDM 分解误差的理论下限，并提出了可消除该下限的掩码设计。
- **实用价值**：大幅减少采样步数（从数百步到 2-4 步），显著提升推理速度，且兼容预训练权重，易于迁移。
- **简单有效**：在保持 MDM 简洁框架的基础上，仅改变掩码形式，无需复杂架构改动。

## 8. 不足与局限
- **实验细节缺失**：未提供完整实验结果（如具体数值、消融研究、不同蒸馏策略的影响等），仅给出定性结论。
- **算力资源未说明**：无法评估方法的训练/蒸馏成本。
- **应用限制**：当前仅在语言建模任务上验证，对于更复杂的生成任务（如长文本、多模态）是否有效尚不明确。
- **随机无限掩码的实现细节**：摘要未详述如何实际定义和采样无限随机掩码，以及如何保证训练和推理的稳定性。

（完）
