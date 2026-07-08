---
title: Improving Sampling for Masked Diffusion Models via Information Gain
title_zh: 通过信息增益改进掩码扩散模型的采样
authors: "Kaisen Yang, Jayden Teoh, Kaicheng Yang, Yitong Zhang, Alex Lamb"
date: 2026-04-30
pdf: "https://openreview.net/pdf/6b6550cec1e7e1aa6b053b6e82a7f9ad848a1b34.pdf"
tags: ["query:diffusion-lm"]
score: 8.0
evidence: 面向掩码扩散模型的无训练采样方法
tldr: 针对掩码扩散模型现有贪婪采样器忽视下游影响导致次优生成的问题，提出Info-Gain采样器。这是一种无训练的解码方法，利用双向结构平衡即时不确定性与剩余掩码位置的信息增益。在推理、编码、创意写作和图像生成任务上，平均推理准确率提升2.9-11.6个百分点。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 掩码扩散模型的现有采样器过于贪婪，导致累积不确定性增加。
method: 提出基于信息增益的无训练采样方法，平衡当前不确定性与未来信息。
result: 在推理、编码和生成任务上准确率提升2.9-11.6个百分点。
conclusion: Info-Gain采样器有效提升了掩码扩散模型的生成质量。
---

## Abstract
Masked Diffusion Models (MDMs) enable flexible decoding orders, yet existing samplers remain largely greedy, selecting locally certain tokens without accounting for their downstream effects. We show that this myopia can increase cumulative uncertainty and lead to suboptimal generation. To address this, we propose the **Info-Gain Sampler**, a training-free decoding method that uses the bidirectional structure of MDMs to balance immediate uncertainty with the information gained over remaining masked positions. Across reasoning, coding, creative writing, and image generation tasks, Info-Gain Sampler consistently outperforms existing MDM samplers, improving average reasoning accuracy by 2.9--11.6 percentage points and achieving a 62.8% average win rate in creative writing. The code is available at https://github.com/yks23/Information-Gain-Sampler.

---

## 论文详细总结（自动生成）

# 论文中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **核心问题**：现有掩码扩散模型（Masked Diffusion Models, MDMs）的采样器过于“贪婪”（greedy），仅选择当前局部置信度高的 token，而忽略了该选择对未来剩余掩码位置的累积不确定性影响。这种短视行为导致整体生成质量次优。
- **背景**：MDMs 支持灵活的解码顺序，但多数采样方法未利用双向结构信息；作者提出需在即时不确定性与剩余位置的信息增益之间取得平衡。

## 2. 论文提出的方法论
- **核心思想**：提出 **Info-Gain 采样器**，一种无需训练的解码方法，利用 MDMs 的双向结构，平衡当前步骤的不确定性与未来剩余掩码位置所能获得的信息增益。
- **关键技术细节**：
  - 在每一步解码时，不仅考虑当前 token 的确定性（基于模型输出概率），还估计该 token 被确定后，剩余掩码位置的不确定性减少量（即信息增益）。
  - 通过评估每个候选 token 对整体生成的预期信息增益，选择能使全局累积不确定性最小化的 token。
- **算法流程**（文字说明）：
  1. 从完全掩码的序列开始。
  2. 对每个未掩码位置，计算模型预测的当前 token 概率分布及该位置确定后剩余掩码位置的熵降。
  3. 贪心地选择使“当前不确定性 + 剩余位置信息增益”权衡最优的 token 进行解码。
  4. 重复直到序列完全生成。

## 3. 实验设计
- **任务与数据集/场景**：涵盖推理任务（如数学、逻辑）、编码任务（代码生成）、创意写作、图像生成等多个领域。
- **Benchmark**：对比了现有 MDMs 采样器（文中提及“existing MDM samplers”，但未列出具体名称）。
- **对比方法**：显然是与贪婪采样基线相比。从结果看，Info-Gain 在所有任务上一致超越。

## 4. 资源与算力
- **文中未明确说明**：论文摘要及元数据中未提及 GPU 型号、数量、训练时长等信息。可能是因为该方法无训练（training-free），仅需一次前向传播计算信息增益，算力消耗相对较低。

## 5. 实验数量与充分性
- **实验数量**：在推理、编码、创意写作、图像生成四大类任务上进行了实验，涉及多个数据集（具体未列出细节）。推理准确率平均提升 2.9～11.6 个百分点，创意写作胜率达到 62.8%。
- **充分性与公平性**：文中提到“consistently outperforms existing MDM samplers”，说明进行了足够多的对比实验；方法本身无训练，避免了调参偏差。但未提供消融研究或敏感性分析，因此充分性略有不足。

## 6. 论文的主要结论与发现
- **主要结论**：Info-Gain 采样器作为一种无需训练的解码策略，能显著提升 MDMs 的生成质量，在多种文本和图像生成任务上均优于贪婪采样。
- **具体发现**：贪婪采样导致的累积不确定性是生成质量瓶颈，引入信息增益可有效缓解。

## 7. 优点（方法与实验设计的亮点）
- **无训练**：无需额外训练或微调，可直接应用于任意预训练 MDMs。
- **理论直观**：从信息论角度切入，平衡局部与全局的 uncertainty。
- **跨领域验证**：覆盖文本（推理、编码、创意写作）和图像生成，验证了通用性。
- **实验分析扎实**：准确率提升幅度明确，且包含胜率指标。

## 8. 不足与局限
- **实验覆盖有限**：具体数据集名称、任务规模未在摘要中详细列出，可能未在超大规模基准（如代码竞赛、图像 FID）上完整评估。
- **偏差风险**：仅对比了现有“greedy”采样器，未对比其他潜在采样策略（如温度采样、top-k、top-p 等），或已有改进方法（如 MC dropout 采样）。
- **应用限制**：需计算每个候选位置的信息增益，计算开销可能高于简单贪婪采样（尤其在长序列或高维图像上）。
- **资源细节缺失**：无法复现算力要求，也未讨论实际部署成本。

（完）
