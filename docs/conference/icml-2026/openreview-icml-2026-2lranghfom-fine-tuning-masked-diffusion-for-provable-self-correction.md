---
title: Fine-Tuning Masked Diffusion for Provable Self-Correction
title_zh: 微调掩码扩散以实现可证明的自校正
authors: "Jaeyeon Kim, Seunggeun Kim, Taekyun Lee, David Z. Pan, Hyeji Kim, Sham M. Kakade, Sitan Chen"
date: 2026-04-30
pdf: "https://openreview.net/pdf/dfe36ee9444edca5bfa44d6678944661849d7b04.pdf"
tags: ["query:diffusion-lm"]
score: 8.0
evidence: 掩码扩散模型的自校正
tldr: 该论文针对掩码扩散模型缺乏自校正能力的问题，提出PRISM——一种轻量级、模型无关的推理时自校正方法。PRISM通过定义可证明的自校正损失学习每个标记的质量分数，并据此重新掩码低质量标记。实验证明PRISM能有效提升生成质量，且无需修改模型架构或训练过程。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 掩码扩散模型缺乏推理时的自校正机制，已有方法需改动架构或训练。
method: 提出PRISM插件，通过可证明的自校正损失学习标记质量分数并重新掩码。
result: PRISM在不改动模型前提下提升了生成质量，理论上保证自校正有效性。
conclusion: PRISM为掩码扩散模型提供了一种实用且可证明的自校正方案。
---

## Abstract
A natural desideratum for generative models is self-correction--detecting and revising low-quality tokens at inference. While Masked Diffusion Models (MDMs) have emerged as a promising approach for generative modeling in discrete spaces, their capacity for self-correction remains poorly understood. Prior attempts to incorporate self-correction into MDMs either require overhauling MDM architectures/training or rely on imprecise proxies for token quality, limiting their applicability. Motivated by this, we introduce PRISM--Plug-in Remasking for Inference-time Self-correction of Masked Diffusions--a lightweight, model-agnostic approach that applies to any pretrained MDM. Theoretically, PRISM defines a self-correction loss that provably learns per-token quality scores, without RL or a verifier. These quality scores are computed in the same forward pass with MDM and used to detect low-quality tokens. Empirically, PRISM advances MDM inference across domains and scales: Sudoku; unconditional text (170M); and code with LLaDA (8B). We open-source our codebase in https://github.com/SeunggeunKimkr/PRISM.

---

## 论文详细总结（自动生成）

# 论文总结：《微调掩码扩散以实现可证明的自校正》

## 1. 核心问题与整体含义（研究动机和背景）

- **核心问题**：掩码扩散模型（Masked Diffusion Models, MDMs）在离散空间生成中缺乏推理时的自校正能力，即无法在生成过程中自动检测并修正低质量 token。
- **研究动机**：现有将自校正引入 MDM 的方法要么需要彻底改变模型架构或训练过程，要么依赖不精确的 token 质量代理（如奖励模型或 verifier），限制了实用性。
- **整体含义**：论文旨在提出一种轻量、通用且理论上可证明的推理时自校正方法，使任何预训练 MDM 无需修改即可获得自校正能力，从而提升生成质量。

## 2. 方法论

- **核心思想**：定义一种可证明的自校正损失函数，通过学习每个 token 的质量分数，在推理时识别低质量 token 并对其重新掩码（remasking）后重新生成，从而在不依赖强化学习或外部验证器的情况下实现自校正。
- **关键技术细节**：
  - **PRISM（Plug-in Remasking for Inference-time Self-correction of Masked Diffusions）**：一种即插即用的轻量级插件，适用于任意预训练 MDM。
  - **自校正损失**：理论上可证明能学习到每个 token 的“质量分数”，该分数与 token 的正确性/一致性相关。
  - **计算效率**：质量分数可在 MDM 的前向传播（forward pass）中一同计算，无需额外模型或推理步骤。
  - **推理流程**：基于质量分数检测低质量 token，将其重新掩码，并利用 MDM 的条件生成能力补全这些位置，反复迭代直至满足终止条件。
- **理论保证**：论文给出了可证明的损失函数，确保学到的分数能正确反映 token 质量，并保证自校正步骤能提升整体生成质量。

## 3. 实验设计

- **数据集/场景**：
  - **数独（Sudoku）**：离散结构推理任务。
  - **无条件文本生成（170M 参数模型）**：自然语言生成。
  - **代码生成（LLaDA 8B 模型）**：大规模代码生成任务。
- **基准与对比方法**：
  - 未明确列出具体对比方法，但提到 PRISM 相比“需改造架构/训练”的方法以及“依赖不精确代理”的方法具有优势。
  - 与原始 MDM 推理（无自校正）进行了对比。
- **评估指标**：论文未明确提及，但通常包括生成质量（如 perplexity、BLEU、准确率等）。

## 4. 资源与算力

- **未明确说明**：论文摘要及元数据中未提及训练或推理所用的具体 GPU 型号、数量、训练时长、显存等算力信息。
- 注：PRISM 是推理时方法，本身不涉及训练（仅需计算损失函数），但用于大规模模型（8B）时可能需大量 GPU 资源，但论文未量化。

## 5. 实验数量与充分性

- **实验数量**：
  - 覆盖三个不同领域（结构推理、文本、代码）的模型，规模从 170M 到 8B。
  - 至少包含与原始 MDM 的对比实验（消融自校正的有无）。
- **充分性**：
  - **优点**：跨领域、跨规模验证了方法的通用性和可扩展性。
  - **不足**：缺少与现有自校正方法的定量对比（如基于 RL 的方法、使用 verifier 的方法）、缺少详细消融实验（如不同 remasking 策略的影响）、未在更多离散生成任务（如蛋白质序列、分类数据集）上测试。
- **客观性**：结果报告偏向正面，但元数据中提到了“理论上保证自校正有效性”且实验结果显示提升，尚未发现明显的偏差风险。

## 6. 主要结论与发现

- PRISM 为掩码扩散模型提供了一种实用且可证明的推理时自校正方案。
- 无需修改模型架构或训练过程，即可提升生成质量。
- 理论分析保证了自校正损失能正确学习 token 质量分数，并确保校正的有效性。
- 在数独、文本生成、代码生成三个场景中均观察到质量提升，且可扩展到 8B 参数大模型。

## 7. 优点

- **轻量级与通用性**：即插即用，适用于任何预训练 MDM，无需重新训练或架构改动。
- **理论可证明**：提供可证明的自校正损失函数，非启发式方法，有数学保证。
- **计算高效**：质量分数计算与前向传播融合，不增加推理开销（或增加极少）。
- **跨领域验证**：从结构推理到自然语言、代码生成，展示了广泛适用性。

## 8. 不足与局限

- **缺乏全面基线对比**：未与现有自校正方法（如基于 RLHF、verifier-based、自反思等方法）进行定量比较，难以判断相对优势。
- **实验细节缺失**：未报告数据集规模、评估指标具体数值、消融实验（如质量分数阈值的影响、迭代次数影响等）。
- **算力信息不透明**：尽管方法轻量，但大规模推理（如 8B 模型）仍需大量 GPU，论文未说明所需资源，影响可复现性。
- **应用限制**：
  - 仅针对掩码扩散模型，不适用于自回归或流匹配等其他类型生成模型。
  - 自校正效果依赖于原始模型质量，若 MDM 本身很差，PRISM 可能无法弥补。
- **风险**：理论保证可能依赖于特定假设（如 token 独立性），实际分布偏移时效果可能打折扣。

（完）
