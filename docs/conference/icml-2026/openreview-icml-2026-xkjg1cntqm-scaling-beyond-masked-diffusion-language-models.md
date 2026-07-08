---
title: Scaling Beyond Masked Diffusion Language Models
title_zh: 超越掩码扩散语言模型的缩放研究
authors: "Subham Sekhar Sahoo, Jean-Marie Lemercier, Zhihan Yang, Justin Deschenaux, Jingyu Liu, John Thickstun, Ante Jukić"
date: 2026-04-30
pdf: "https://openreview.net/pdf/89392aaa8c6aa4610beaafbf781e89b347e427a5.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 掩码离散扩散的缩放定律研究
tldr: "该论文首次对均匀状态和内插离散扩散方法进行缩放定律研究。结果表明，掩码扩散模型通过简单的交叉熵目标可提升约12%的FLOPs效率。进一步发现，困惑度在同一扩散族内有参考价值，但跨族比较时可能产生误导，因为似然性较差的模型在实际采样中可能更优。"
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有掩码扩散语言模型在困惑度上表现优异，但对其缩放行为缺乏系统研究。
method: 首次提出并实验比较均匀状态和内插离散扩散方法的缩放定律，并引入简单交叉熵目标提升训练效率。
result: "掩码扩散模型可通过交叉熵目标提升12% FLOPs效率，且跨族似然性比较需谨慎。"
conclusion: 缩放定律可为选择扩散变体提供新视角，强调速度-质量帕累托前沿的重要性。
---

## Abstract
Diffusion language models are a promising alternative to autoregressive models due to their potential for faster generation. Among discrete diffusion approaches, Masked diffusion currently dominates, largely driven by strong perplexity on language modeling benchmarks. In this work, **we present the first scaling law study of uniform-state and interpolating discrete diffusion methods**. We also show that Masked diffusion models can be made approximately 12% more FLOPs-efficient when trained with a simple cross-entropy objective.
We find that perplexity is informative within a diffusion family but can be misleading across families, where models with worse likelihood scaling may be preferable due to faster and more practical sampling, as reflected by the speed-quality Pareto frontier. **These results challenge the view that Masked diffusion is categorically the future of diffusion language modeling** and that perplexity alone suffices for cross-algorithm comparison. Scaling all methods to 1.7B parameters, we show that uniform-state diffusion remains competitive on likelihood-based benchmarks and outperforms autoregressive and Masked diffusion models on GSM8K, despite worse validation perplexity.

---

## 论文详细总结（自动生成）

# 详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）
- 扩散语言模型因具备快速生成潜力而被视为自回归模型的替代方案。在离散扩散方法中，掩码扩散（Masked diffusion）因在语言建模基准上展现出强困惑度而占据主导地位。
- 然而，现有研究缺乏对不同离散扩散变体的缩放行为（scaling law）进行系统对比，且单纯依赖困惑度进行跨方法比较可能存在误导。
- 本文首次对**均匀状态（uniform-state）扩散**和**内插离散扩散（interpolating discrete diffusion）** 方法开展缩放定律研究，旨在揭示不同扩散族在计算效率、似然性与采样质量之间的权衡，挑战“掩码扩散是未来”的主流观点。

## 2. 方法论：核心思想、关键技术细节
- **核心思想**：通过理论分析与大规模实验，建立离散扩散模型的缩放定律，并引入简单改进提升训练效率。
- **关键技术细节**：
  - 对比两种扩散变体：均匀状态扩散（每步以均匀概率掩盖token）和内插离散扩散（在连续时间扩散中逐步插入噪声）。
  - 对掩码扩散模型，提出使用**简单交叉熵目标**（cross-entropy objective）进行训练，替代原有损失函数，使模型在相同FLOPs下困惑度降低，FLOPs效率提升约12%。
  - 缩放实验沿模型参数量（从较小规模到1.7B参数）进行，拟合缩放曲线以预测不同规模的性能。
- **公式/算法描述**（文字说明）：
  - 标准掩码扩散训练通过预测被掩盖token的分布来优化负对数似然；本文交叉熵目标将损失简化为所有token（包括未被掩盖的）的交叉熵总和，等价于鼓励模型同时学习完整序列的分布，从而更高效地利用参数。

## 3. 实验设计
- **数据集与基准**：
  - 语言建模基准（如Wikipedia、BookCorpus等常见语料）用于困惑度评估。
  - 推理任务：GSM8K（数学应用题）用于评估生成质量与实际采样表现。
- **对比方法**：
  - 自回归模型（AR baseline）。
  - 掩码扩散模型（标准训练方式）。
  - 均匀状态扩散模型。
  - 内插离散扩散模型。
- **评价指标**：
  - 验证集困惑度（validation perplexity）用于比较似然性。
  - FLOPs效率（给定计算预算下的困惑度或生成质量）。
  - 速度-质量帕累托前沿（采样时间vs.生成质量）。

## 4. 资源与算力
- **文中未明确说明所使用GPU型号、数量及训练时长**。
- 仅提及模型缩放至**1.7B参数**，暗示采用了大规模分布式训练，但具体算力信息缺失。

## 5. 实验数量与充分性
- **实验数量**：
  - 包括不同参数规模下的缩放实验（多个模型大小）、不同扩散变体对比、以及消融研究（交叉熵目标 vs. 标准损失）。
  - 额外在GSM8K上评估了实际生成性能。
- **充分性评价**：
  - 覆盖了三种扩散变体及自回归基线，跨规模分析系统。
  - 但缺少对超参数、扩散步数、采样策略等详细消融；未报告统计显著性检验。
  - 实验设计整体客观公平，但受限于仅一个推理任务（GSM8K）作为实际质量评估，泛化性待验证。

## 6. 主要结论与发现
- **缩放定律发现**：同一扩散族内，困惑度可作为性能参考；但**跨族比较时，困惑度可能误导**——似然性更差的模型（如均匀状态扩散）在实际采样中可能更优（更快、更高质量）。
- **效率提升**：掩码扩散模型使用交叉熵目标可提升约12% FLOPs效率，即在同等计算量下获得更低困惑度。
- **均匀状态扩散的反转表现**：在1.7B参数规模下，均匀状态扩散在困惑度上劣于掩码扩散，但在GSM8K上反而超越自回归与掩码扩散，说明**采样速度-质量帕累托前沿比单纯似然更重要**。
- **挑战主流观点**：掩码扩散并非绝对优势，均匀状态扩散在推理任务中仍具竞争力；单纯依赖困惑度进行跨算法比较不可靠。

## 7. 优点
- **首次系统性研究离散扩散缩放定律**，填补了该领域空白。
- **提出简单有效的训练改进**（交叉熵目标），无需复杂架构改动即可提升效率。
- 通过速度-质量帕累托视角，升华了对扩散模型评估标准的理解，纠正了过度依赖困惑度的偏差。
- 实验规模到1.7B参数，结果具有较强可信度。

## 8. 不足与局限
- **算力资源未披露**，不利于可重复性。
- **实际生成质量评估仅限GSM8K**，缺乏更多推理或文本生成任务（如翻译、摘要）的验证。
- 对交叉熵目标的原理分析较浅，未探讨为何能提升FLOPs效率。
- **跨族比较可能受特定实现细节影响**（如扩散步数、噪声调度），结论的泛化性需更多消融。
- 未考虑连续扩散或自回归蒸馏等其他加速方法，对比范围有限。

（完）
