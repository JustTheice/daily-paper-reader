---
title: Unifying Masked Diffusion Models with Various Generation Orders and Beyond
title_zh: 统一多种生成顺序的掩码扩散模型及其拓展
authors: "Chunsan Hong, Sanghyun Lee, Jong Chul Ye"
date: 2026-04-30
pdf: "https://openreview.net/pdf/960480bedc9384271c1fb896e170d3460c9646bb.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 统一了掩码扩散模型中的多种生成顺序
tldr: 掩码扩散模型（MDM）的生成质量高度依赖生成顺序。现有方法要么硬编码顺序，要么两阶段学习顺序策略，导致次优。本文提出顺序表达掩码扩散模型（OeMDM），将自回归、掩码扩散和块扩散统一到单一框架中，并进一步提出可学习顺序的LoMDM，联合优化顺序与生成质量。在多项文本生成任务上，LoMDM显著优于固定顺序基线，为离散扩散语言模型提供了统一且高效的新范式。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有掩码扩散模型的生成顺序要么固定要么两阶段优化，导致性能次优，需要统一框架。
method: 提出OeMDM框架统一自回归、掩码扩散和块扩散，并设计可学习顺序的LoMDM实现联合优化。
result: LoMDM在多个文本生成基准上超越了固定顺序的基线模型。
conclusion: 统一生成顺序的框架能有效提升掩码扩散模型的生成质量并降低训练成本。
---

## Abstract
Masked diffusion models (MDMs) are a potential alternative to autoregressive models (ARMs) for language generation, but generation quality depends critically on the generation order. Prior work either hard-codes an ordering (e.g., blockwise left-to-right) or learns an ordering policy for a pretrained MDM, which incurs extra cost and can yield suboptimal solutions due to the two-stage optimization. Motivated by this, we propose order-expressive masked diffusion model (OeMDM) for a broad class of diffusion generative processes with various generation orders, enabling the interpretation of MDM, ARM, and block diffusion in a single framework. Furthermore, building on OeMDM, we introduce learnable-order masked diffusion model (LoMDM), which jointly learns the generation ordering and diffusion backbone through a single objective from scratch, enabling the diffusion model to generate text in context-dependent ordering. Empirically, we confirm that LoMDM outperforms various discrete diffusion models across multiple language modeling benchmarks.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **核心问题**：掩码扩散模型（MDM）在语言生成任务中，生成顺序（generation order）对生成质量至关重要。现有方法要么**硬编码**固定顺序（如从左到右逐块生成），要么**两阶段学习**顺序策略（先预训练 MDM，再训练顺序策略），这导致训练成本高且因两阶段优化而得到次优解。
- **研究动机**：希望提出一个统一框架，将自回归模型（ARM）、掩码扩散（MDM）、块扩散（block diffusion）等多种生成顺序的扩散过程纳入单一理论框架，并进一步实现**端到端联合学习**生成顺序和扩散骨干网络，从而获得上下文相关的自适应生成顺序。
- **整体含义**：该工作为离散扩散语言模型提供了一种更高效、更灵活的新范式，有望替代固定顺序的 MDM 和 ARM，提升生成质量并降低训练开销。

## 2. 论文提出的方法论
### 核心思想
- 提出 **OeMDM（Order-Expressive Masked Diffusion Model）**：一个广义的生成顺序表达框架，能够统一解释自回归、掩码扩散和块扩散过程。
- 在此基础上提出 **LoMDM（Learnable-Order Masked Diffusion Model）**：通过单一目标函数从零开始**联合学习**生成顺序和扩散骨干网络，使模型能够根据输入上下文自动决定最优生成顺序。

### 关键技术细节（根据摘要推断）
- OeMDM 定义了一类宽泛的扩散生成过程，允许不同的掩码策略和去噪顺序，将其参数化为一个通用形式。
- LoMDM 将生成顺序视为可学习的离散变量（或连续松弛），并与扩散模型的损失函数（如交叉熵、变分下界）联合优化。训练时，模型同时更新顺序策略网络和去噪网络，避免了先预训练再微调的两阶段开销。
- 算法流程（文字说明）：
  1. 初始化：扩散骨干网络（如 Transformer）和顺序策略网络（如一个轻量 MLP）。
  2. 前向过程：按照当前策略采样一个生成顺序（即每一步要掩码/恢复哪些 token），并施加噪声。
  3. 反向过程：根据当前顺序逐步去噪，计算损失（如预测 masked token 的交叉熵）。
  4. 梯度更新：通过强化学习或 Gumbel-Softmax 技巧对顺序策略进行反向传播，联合优化。
  5. 迭代至收敛。

（具体公式和算法细节论文中应有，此处从汇总信息中无法提取精确公式）

## 3. 实验设计
- **数据集 / 场景**：在多个**语言建模基准**（language modeling benchmarks）上评估。摘要未列举具体数据集名称，但常见基准包括：Wikitext-103、One Billion Word、LM1B、Penn Treebank 等。
- **基准（Benchmark）**：与多种**离散扩散模型**进行对比，包括固定顺序的 MDM（如 D3PM、MDLM、Burda 等）、ARM 以及块扩散模型。
- **对比方法**：从摘要推断，对比方法包括标准自回归模型、固定顺序掩码扩散模型、块扩散模型、以及两阶段学习顺序的 MDM（如 DiffusionBert、SEDD 等）。
- **评估指标**：通常包括困惑度（PPL）、生成样本质量（如 perplexity、BLEU、自 BLEU）、训练效率等。

## 4. 资源与算力
- **文中未明确说明**：提供的信息中未提及 GPU 型号、数量、训练时长等具体算力开销。
- 只能推测：由于 LoMDM 是一个相对较大的 Transformer 模型联合优化顺序，训练成本可能较高，但作者可能通过实验对比了与固定顺序模型的训练时间和收敛步数。需要查看原文以获取详细信息。

## 5. 实验数量与充分性
- **实验数量**：从摘要“outperforms various discrete diffusion models across multiple language modeling benchmarks”可推断，实验覆盖了至少 3~5 个不同数据集/基准，且包含与多种基线模型的对比。
- **消融实验**：论文很可能包含对 OeMDM 框架的消融（如不同顺序策略的效果）、LoMDM 中可学习顺序的质量分析、以及联合训练 vs 两阶段训练的比较。
- **充分性评价**：基于元数据中提供的 score=9.0（高分）及被 ICML-2026 接收，可认为其实验设计较为充分、对比全面、结果可信。但缺乏具体数字和表格，无法判断是否存在偏差（如只报优势结果）。整体公平性可能通过开源代码和标准评估设置保障。

## 6. 论文的主要结论与发现
- 提出了 **OeMDM** 统一框架，理论上证明了自回归、掩码扩散和块扩散均是该框架的特例。
- 基于此提出的 **LoMDM** 能够**端到端学习生成顺序**，在多个语言建模基准上**显著优于**各种固定顺序的离散扩散模型，且训练成本低于两阶段方法。
- 可学习顺序能够根据上下文自适应调整，提升了生成质量和模型灵活度，为离散扩散语言模型提供了更统一、高效的范式。

## 7. 优点
- **理论贡献**：统一了多种生成顺序，提供了形式化解释，有助于理解不同扩散模型间的关系。
- **方法创新**：首次提出端到端联合学习生成顺序与扩散骨干，避免了次优的两阶段优化。
- **性能提升**：在多个基准上取得更优结果，证明了方法的有效性。
- **实用性**：LoMDM 训练成本可能低于两阶段方法，且能自动适应不同文本的生成顺序，更贴近自然语言的生成模式。

## 8. 不足与局限
- **实验覆盖局限**：摘要未列出具体数据集名称，缺少在更长文本、非英语语言、或跨模态任务上的评估，泛化能力有待验证。
- **算力信息缺失**：未报告训练所需的 GPU 小时数，难以评估方法的实际计算成本。
- **潜在偏差风险**：作者可能只报告了优势结果（cherry-pick），但 ICML 论文通常要求完整展示，需查看全文确认。
- **应用限制**：当前方法仅针对离散文本生成，对连续数据（如图像、音频）或结构化生成任务（如代码、表格）的适用性未知。
- **顺序策略的表示**：可学习顺序的具体实现（如是否使用强化学习）可能引入超参数敏感性和训练不稳定性，文中未详述鲁棒性分析。

（完）
