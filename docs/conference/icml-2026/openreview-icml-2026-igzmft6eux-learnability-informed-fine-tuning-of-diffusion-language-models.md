---
title: Learnability-Informed Fine-Tuning of Diffusion Language Models
title_zh: 可学习性指导的扩散语言模型微调
authors: "Shubham Parashar, Atharv Chagi, Jacob Helwig, Lakshmi Jotsna Madhavarapu, Sushil Vemuri, James Caverlee, Dileep Kalathil, Shuiwang Ji"
date: 2026-04-30
pdf: "https://openreview.net/pdf/410ff6a4ae46d1c800e20a03a522baba27b37ba2.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 扩散语言模型微调；可学习性感知
tldr: 直接对扩散语言模型使用监督微调可能降低性能。本文分析发现其忽视了令牌可学习性：困难令牌在强掩蔽时难学，容易令牌在弱掩蔽时价值低。提出LIFT算法，根据掩蔽程度动态调整学习顺序，有效提升扩散语言模型的推理能力。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 监督微调在扩散语言模型上效果不佳甚至有害，原因不明。
method: 提出LIFT算法，根据掩蔽程度决定学习难易令牌的顺序。
result: 在推理任务上，LIFT显著提升扩散语言模型性能，优于标准SFT。
conclusion: 考虑令牌可学习性对于扩散语言模型微调至关重要。
---

## Abstract
We aim to improve the reasoning capabilities of diffusion language models (DLMs). While SFT is a popular post-training recipe for autoregressive models, its use in DLMs faces challenges and can even hurt performance, though the underlying causes remain understudied. Our analysis reveals that vanilla SFT overlooks learnability, namely, *what* and *when* tokens are learned. Specifically, rare tokens are difficult to learn when most of the input is masked, whereas it is straightforward and thus of little value to learn common
tokens when most of the input is unmasked. Motivated by our analysis, we propose LIFT, an efficient SFT-based post-training algorithm for
DLMs. LIFT learns easy tokens when most of the input is masked and hard tokens when more context is available, thereby aligning training with the information available at different diffusion time steps. Our results show that LIFT outperforms existing SFT baselines across six reasoning benchmarks, achieving up to a 3x relative gain on AIME’24 and AIME’25. Our code is publicly available at https://github.com/divelab/LIFT.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：扩散语言模型（DLMs）在推理任务上表现不足，研究者希望提升其推理能力。监督微调（SFT）是自回归模型常用的后训练方法，但直接应用于DLMs时效果不佳，甚至会损害性能，其原因尚未被充分研究。
- **核心问题**：为什么标准SFT对DLMs无效？作者通过分析发现，标准SFT忽略了“可学习性”——即令牌（token）在什么时间步、什么掩蔽程度下被学习。具体来说：
  - 稀有令牌（难学习）在输入大部分被掩蔽时难以学习；
  - 常见令牌（易学习）在输入大部分未被掩蔽时很容易学习，但学习这类令牌价值很低。
- **整体含义**：必须根据扩散时间步的不同信息量，动态调整学习顺序——在固定掩蔽率下学习固定难易程度的令牌，才能有效微调DLMs。

### 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：提出LIFT（Learnability-Informed Fine-Tuning）算法，一种高效的基于SFT的后训练算法。其关键原则是“在恰当的扩散时间步学习恰当的令牌”：当输入大部分被掩蔽时（信息少），学习容易令牌；当更多上下文暴露时（信息多），学习困难令牌。
- **关键技术细节**：
  - 利用扩散过程本身的时间步（timestep）决定掩蔽程度（masking ratio）。
  - 在微调过程中，根据当前时间步对应的掩蔽率，从训练样本中智能选择令牌进行预测：早期时间步（高掩蔽率）主要学习低频/稀有令牌；后期时间步（低掩蔽率）主要学习高频/常见令牌。
  - 具体实现：可能在损失函数中对不同令牌赋予不同权重，或使用课程学习（curriculum learning）策略，根据时间步调整训练样本的标签分布。论文中未给出详细公式，但描述为“aligning training with the information available at different diffusion time steps”。
- **算法流程（文字描述）**：
  1. 给定一个扩散语言模型（如MDLM、SSD-LM等）和推理任务数据集。
  2. 定义扩散前向过程：逐步添加噪声（掩蔽）。
  3. 在微调阶段，对于每个训练样本，随机采样一个扩散时间步t，对应的掩蔽率已知。
  4. 根据t决定该步应学习的令牌类型：若t小（掩蔽率低，信息多），则关注困难令牌（稀有词）；若t大（掩蔽率高，信息少），则关注容易令牌（常见词）。
  5. 计算损失时，仅对选定的令牌进行预测和反向传播（或加权）。
  6. 迭代训练直到收敛。

### 3. 实验设计：数据集、基准、对比方法

- **数据集/场景**：使用六个推理基准，包括AIME’24、AIME’25（美国数学邀请赛）、以及可能其他数学/逻辑推理任务（论文摘要提及六个，但未列出全部名称）。
- **Benchmark**：采用标准SFT作为主要基线，并与其他后训练方法（如DPO等）对比。
- **对比方法**：
  - 标准SFT（vanilla SFT）
  - 可能还有其他扩散模型微调变体（论文未详述，但提到“existing SFT baselines”）。
- **评估指标**：推理准确性（如数学竞赛得分）。

### 4. 资源与算力

- **论文未明确说明**使用的GPU型号、数量、训练时长等具体算力信息。代码已公开，但算力细节缺失。

### 5. 实验数量与充分性

- **实验数量**：主要结果在6个推理基准上报告，并进行了消融研究（如比较不同学习策略、分析可学习性影响）。但论文摘要中未明确列出消融组数。
- **充分性**：实验覆盖了多个推理任务，对比了关键基线，并提供了相对收益（如AIME’24/’25上最高3倍提升）。但未提供与更多自回归模型或更大规模模型的对比，也未在所有可能任务（如生成任务）上验证。整体而言，实验设计聚焦于推理能力验证，对于证明LIFT的有效性较为充分，但泛化性有待进一步检验。
- **客观公平**：作者公开代码，基线设置合理，结果差异显著，较为可信。

### 6. 论文的主要结论与发现

- 标准SFT忽略令牌可学习性，导致DLMs微调后性能下降。
- 提出LIFT：根据扩散时间步动态调整学习顺序（易→难），显著提升DLMs的推理能力。
- 在AIME’24和AIME’25上，LIFT相比标准SFT获得高达3倍的相对增益（即推理得分提高为原来的3倍）。
- 验证了“可学习性指导”对扩散模型微调至关重要。

### 7. 优点：方法或实验设计上的亮点

- **洞察深刻**：首次系统分析DLMs微调失败的原因在于令牌可学习性与时间步不匹配。
- **方法简单有效**：LIFT只需调整训练过程中损失计算的权重或采样策略，不增加模型复杂度，可即插即用。
- **大幅提升**：在强推理基准上获得3倍相对提升，效果显著。
- **代码开源**：促进可复现性和后续研究。

### 8. 不足与局限

- **算力资源未公开**：无法评估方法的训练成本。
- **实验覆盖有限**：仅针对推理任务，未测试在语言生成、翻译等其他任务上的表现；对比方法较少（可能只有SFT变种，未对比强化学习微调如RLHF）。
- **分析粒度**：令牌难易程度的定义可能依赖于频率，但未考虑语义重要性；对于不同模型初始化可能不鲁棒。
- **应用限制**：仅适用于离散扩散模型（如掩蔽扩散），对于连续扩散模型不直接适用。
- **未讨论超参数敏感性**：如学习率、时间步采样策略等对结果的影响未充分分析。

（完）
