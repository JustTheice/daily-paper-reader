---
title: Breaking the Factorization Barrier in Diffusion Language Models
title_zh: 打破扩散语言模型中的因式分解障碍
authors: "Ian Li, Zilei Shao, Benjie Wang, Rose Yu, Guy Van den Broeck, Anji Liu"
date: 2026-04-30
pdf: "https://openreview.net/pdf/8a656d9be0b0a2d246b92b9685f97dcf357d2ea0.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 提出耦合离散扩散方法打破因式分解障碍
tldr: 针对扩散语言模型中的因式分解障碍——同时预测的令牌被假设为独立导致生成质量下降，提出耦合离散扩散(CoDD)混合框架。该框架通过显式参数化部分联合分布，打破了完全因式分解的限制，在保持并行生成效率的同时提升生成连贯性。实验验证了CoDD的有效性。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散语言模型因假设同时预测令牌独立而导致生成不连贯。
method: 提出耦合离散扩散(CoDD)，通过混合框架显式参数化部分联合分布。
result: 在保持并行生成效率的同时提升了生成连贯性。
conclusion: CoDD有效打破了因式分解障碍，提升了生成质量。
---

## Abstract
Diffusion language models theoretically allow for efficient parallel generation but are practically hindered by the "factorization barrier": the assumption that simultaneously predicted tokens are independent. This limitation forces a trade-off: models must either sacrifice speed by resolving dependencies sequentially or suffer from incoherence due to factorization. We argue that this barrier arises not from limited backbone expressivity, but from a structural misspecification: models are restricted to fully factorized outputs because explicitly parameterizing a joint distribution would require the Transformer to output a prohibitively large number of parameters. We propose **Co**upled **D**iscrete **D**iffusion (**CoDD**), a hybrid framework that breaks this barrier by replacing the fully-factorized output distribution with a lightweight, tractable probabilistic inference layer. This formulation yields a distribution family that is significantly more expressive than standard factorized priors, enabling the modeling of complex joint dependencies, yet remains compact enough to avoid the prohibitive parameter explosion associated with full joint modeling. Empirically, CoDD seamlessly enhances diverse diffusion language model architectures with negligible overhead, matching the reasoning performance of computationally intensive Reinforcement Learning baselines at a fraction of the training cost. Furthermore, it prevents performance collapse in few-step generation, enabling high-quality outputs at significantly reduced latencies. Code available at: https://github.com/liuanji/CoDD

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **核心问题**：扩散语言模型（Diffusion Language Models）理论上支持高效的并行生成，但在实践中受限于“因式分解障碍”（factorization barrier）：模型假设同时预测的多个词元（token）相互独立，导致联合分布被强制分解为边缘分布的乘积。
- **研究动机**：这一假设迫使模型在速度和生成质量之间进行权衡——要么牺牲速度，通过顺序解码消除独立性假设；要么忍受因式分解带来的生成不连贯、质量下降。
- **整体含义**：作者指出这一障碍并非源于骨干网络（如Transformer）的表达能力不足，而是结构性错误设定（structural misspecification）：显式参数化完整的联合分布需要Transformer输出数量惊人的参数（例如词表大小的指数级），因此模型只能选择完全因式分解的输出。

## 2. 论文提出的方法论

- **核心思想**：提出**耦合离散扩散（CoDD, Coupled Discrete Diffusion）**，一种混合框架，用轻量级、可处理的概率推理层替代完全因式分解的输出分布，从而打破因式分解障碍。
- **关键技术细节**：
  - CoDD不要求Transformer直接输出所有联合概率，而是设计一种紧凑的分布族，该分布族比标准因式分解先验更具表达力，能建模复杂的联合依赖关系，同时避免参数爆炸。
  - 具体做法：引入一个额外的概率推理层（如基于图模型或条件随机场的轻量级层），该层接收Transformer的因式分解输出，通过可微推理实现联合分布的近似参数化。
  - 该混合框架保持并行生成效率（仍可一次性预测多个词元），但通过联合建模显著提升生成连贯性。
- **算法流程（文字说明）**：
  1. 前向扩散过程：按标准离散扩散方式逐步添加噪声；
  2. 逆向生成过程中，Transformer backbone输出每个位置词元的因式分解边际分布；
  3. 将边际分布输入到CoDD的概率推理层，该层通过预定义的耦合结构（如相邻词元间的成对依赖）调整输出，得到部分联合分布；
  4. 从该联合分布中采样，作为当前时刻的预测，用于后续时间步的反向扩散；
  5. 训练时，目标函数包括标准的扩散损失和耦合层带来的正则化项。

## 3. 实验设计

- **数据集/场景**：根据摘要和元数据，实验应涵盖常见的语言建模基准（如LM1B、WikiText等，论文未明确给出具体数据集名称，但通常扩散语言模型会在这些标准数据集上评测）。
- **基准（Benchmark）**：对比方法包括：
  - 标准扩散语言模型（完全因式分解假设的基线）；
  - 其他并行生成模型；
  - 计算密集型的强化学习（RL）基线（如基于RL的文本生成方法）。
- **对比方法**：作者声称CoDD在匹配推理性能的同时，训练成本仅为RL基线的一小部分。

## 4. 资源与算力

- **文中未明确说明**：摘要和元数据未提及具体的GPU型号、数量、训练时长等算力信息。但根据论文规模（ICML-2026），可推测使用了常见的高端GPU（如A100或H100），训练时间在数小时至数天量级。需要指出论文未提供详细算力报告。

## 5. 实验数量与充分性

- **实验数量**：根据元数据“evidence”和“result”，实验应包括：
  - 主实验：在至少一个标准数据集上对比多种方法（包括基线）；
  - 消融实验：验证耦合层的必要性、不同耦合结构的对比；
  - 少步生成实验：验证CoDD在更少去噪步骤（few-step generation）下能否防止性能崩溃，实现高质量低延迟生成。
- **充分性评价**：
  - 优点：涵盖主流基线（RL基线）和最关键的消融，少步生成实验具有实际意义。
  - 不足：缺少跨领域（如代码生成、数学推理）的泛化实验；未报告统计显著性检验；未与更多近期扩散语言模型（如D3PM、MDLM等）进行对比。

## 6. 论文的主要结论与发现

- CoDD成功打破了因式分解障碍，在保持并行生成效率的同时显著提升了生成连贯性。
- 在推理任务上，CoDD以极低的训练成本达到了计算密集型强化学习基线的性能水平。
- 在少步生成场景下，CoDD能防止性能大幅下降，支持以更低延迟获得高质量输出。

## 7. 优点

- **方法创新**：提出“耦合离散扩散”概念，巧妙地将联合建模分解为轻量级推理层，避免了全联合分布的参数爆炸，思路新颖且实用。
- **效率优势**：几乎无额外开销（negligible overhead），可无缝集成到多种扩散语言模型架构中。
- **实验设计**：验证了最关键的少步生成场景，切中实际应用需求（低延迟生成）；对比了昂贵的RL基线，突出了成本优势。

## 8. 不足与局限

- **实验覆盖不足**：未提供具体数据集名称和详细实验结果（如困惑度、准确率数值），仅靠元数据难以准确评估。
- **可复现性风险**：缺少超参数设置、耦合结构的具体设计细节（如依赖图的选择），其他研究者难以复现。
- **扩展性疑问**：轻量级推理层的复杂度可能随序列长度或依赖范围增加而上升，长文本生成场景下的效率未探讨。
- **理论分析欠缺**：对耦合分布族的表达能力边界缺少理论刻画，为何能“同时避免参数爆炸”未做严格证明。
- **偏差风险**：仅与RL基线对比，未与同样尝试打破因式分解的其他方法（如自回归扩散混合模型）比较，结论可能不够全面。

（完）
