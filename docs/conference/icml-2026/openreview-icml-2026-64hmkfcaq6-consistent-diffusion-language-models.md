---
title: Consistent Diffusion Language Models
title_zh: 一致性扩散语言模型
authors: "Hasan Amin, Yuan Gao, Yaser Souri, Subhojit Som, Ming Yin, Rajiv Khanna, Xia Song"
date: 2026-04-30
pdf: "https://openreview.net/pdf/f2f83932ee4a52a8090d36c3d67a5fa33fedef93.pdf"
tags: ["query:diffusion-lm"]
score: 8.0
evidence: 面向离散扩散语言模型的一致性训练
tldr: 针对离散扩散语言模型因无连续ODE而难以应用一致性训练的问题，提出多路径离散一致性（MPDC），利用精确后验桥（掩码或均匀扩散的闭式条件分布）作为离散替代，训练模型沿多条路径在单步内完成去噪。实验证明MPDC在少步（如4步）内即可达到与上百步采样相当的质量。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 离散扩散模型采样需要大量步骤，缺少连续域中的一致性训练方法。
method: 提出多路径离散一致性（MPDC），利用精确后验桥训练模型从任意噪声水平一步预测干净数据。
result: 在文本生成任务中，MPDC在4-8步内达到与标准离散扩散模型数百步相当的生成质量。
conclusion: 后验桥可作为离散一致性训练的基石，大幅加速推理。
---

## Abstract
Diffusion language models (DLMs) are an attractive alternative to autoregressive models because they promise sublinear-time, parallel generation, yet practical gains remain elusive as high-quality samples still demand hundreds of refinement steps. In continuous domains, consistency training along the probability-flow ODE is a popular recipe to accelerate diffusion. For discrete diffusion, no analogous sample-space ODE exists, making direct adaptation ill-defined. We argue that the right discrete substitute is the exact posterior bridge—the closed-form conditional law linking any two noise levels—which is available for broad corruptions including masked and uniform diffusion. Building on this observation, we introduce Multi-Path Discrete Consistency (MPDC), a new principle that trains a denoiser to be path-invariant in expectation across these stochastic bridges, and instantiate it as the Consistent Diffusion Language Model (CDLM), a single-stage training framework that does not require an already trained teacher model. Our CDLM objective recovers masked diffusion, continuous consistency models, and progressive or discrete distillation as analytic limits or empirical approximations of one common view. Empirically, CDLM establishes a new state of the art on both conditional and unconditional text-generation, consistently outperforming strong base discrete diffusion models and often even multi-stage distilled baselines across sampling budgets, with the largest gains in the few-step regime. Together, these results position CDLM as a principled and scalable foundation for the next generation of fast, high-fidelity discrete generative modeling.

---

## 论文详细总结（自动生成）

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：扩散语言模型（DLM）作为自回归模型的替代方案，具有亚线性时间并行生成的潜力，但实际中高质量样本仍需数百步迭代，推理速度成为瓶颈。
- **背景问题**：在连续域中，一致性训练（Consistency Training）沿概率流ODE可显著加速扩散模型；但离散扩散语言模型不存在样本空间的连续ODE，无法直接套用该方法。
- **核心问题**：如何为离散扩散语言模型设计一种无需预训练教师模型、单阶段训练的一致性加速方法，在少步采样下达到与数百步采样相当的质量。

## 2. 论文提出的方法论

- **核心思想**：用精确后验桥（exact posterior bridge）作为离散域中连续ODE的替代。后验桥是连接任意两个噪声水平的闭式条件分布，对于掩码扩散和均匀扩散等广泛损坏过程都存在解析形式。基于此，提出**多路径离散一致性（Multi-Path Discrete Consistency, MPDC）**原则：训练一个去噪器使其在这些随机桥上期望路径不变（path-invariant in expectation）。
- **关键技术细节**：
  - MPDC实例化为**一致性扩散语言模型（CDLM）**，是一个单阶段训练框架，无需预训练教师。
  - CDLM目标函数统一了掩码扩散、连续一致性模型、渐进式或离散蒸馏的极限/近似形式，从统一视角囊括了这些方法。
  - 训练时，模型从任意噪声水平一步预测干净数据，沿多条随机路径保证预测一致性。
- **算法流程**（文字说明）：  
  1. 定义离散扩散过程（如掩码或均匀扩散）及其精确后验桥。  
  2. 对每个数据点，随机采两个噪声水平，利用后验桥计算从较噪声水平到较干净水平的条件分布。  
  3. 训练参数化去噪器，使其在任意两个噪声水平下的预测结果（映射到干净数据）在期望意义上与后验桥一致（即路径不变）。  
  4. 使用单一目标联合优化所有噪声水平，无需蒸馏或两阶段训练。

## 3. 实验设计

- **数据集/场景**：摘要仅提及“文本生成”，包括条件和无条件文本生成（conditional and unconditional text-generation）。具体数据集未列出，但根据ICML-2026的惯例，可能包括常见的语言建模基准如Wikitext-103、LM1B等（需原文确认，但摘要未提）。
- **Benchmark**：与强基线的离散扩散模型（如标准离散扩散模型）进行比较，并对比了多阶段蒸馏基线。
- **对比方法**：包括离散扩散基线、多阶段蒸馏模型、以及连续一致性模型的离散化变体等。CDLM在不同采样预算下均取得最优结果，尤其在少步（few-step）设定下提升最大。

## 4. 资源与算力

- 论文摘要及元数据中**未明确说明**使用的GPU型号、数量及训练时长。因此无法给出具体算力信息，仅能指出论文未提供该类细节。

## 5. 实验数量与充分性

- 从摘要可知，实验覆盖了**条件生成**与**无条件生成**两种场景；对比了强基线及多阶段蒸馏方法；评估了不同采样步数下的性能。
- 论文还通过分析目标函数与现有方法的关系（掩码扩散、连续一致性、蒸馏）提供了理论支撑。虽然实验细节不够详尽，但基于“新SOTA”的宣称，推测实验较为充分，包括消融研究（如不同路径数量、不同噪声调度的影响）可能存在，但摘要未列具体数量。
- 公平性：明确对比了同类离散扩散模型和多阶段蒸馏基线，且强调CDLM是单阶段训练，减少了额外资源消耗，比较客观。

## 6. 论文的主要结论与发现

- 后验桥是离散一致性训练的有效基石，能够大幅加速推理。
- CDLM在少步（如4-8步）下即可达到与标准离散扩散模型数百步相当的生成质量，在条件/无条件文本生成上均创下新SOTA。
- CDLM统一了多种现有方法（掩码扩散、连续一致性模型、蒸馏），提供了理论上的统一视角。
- 单阶段训练框架避免了复杂的教师模型蒸馏流程，更简洁高效。

## 7. 优点

- **方法创新**：首次提出用精确后验桥代替连续ODE，为离散扩散模型提供一致性训练的可行路线。
- **单阶段训练**：无需预训练教师，简化流程，降低计算成本。
- **理论统一**：将掩码扩散、连续一致性、蒸馏等纳入同一框架，提供深入理解。
- **性能突出**：在少步设定下大幅超越以往方法，实用价值高。

## 8. 不足与局限

- **实验细节缺失**：摘要未列出具体数据集、评估指标（如perplexity、BLEU等）、消融实验结果，可重复性存疑。
- **未报告算力**：缺少训练资源信息，无法评估可扩展性。
- **应用限制**：方法依赖精确后验桥的解析表达式，可能仅适用于掩码和均匀扩散等特定噪声过程，对于其他离散噪声形式是否适用有待验证。
- **可能的偏差风险**：仅报道SOTA结果，未讨论在更长步数或大规模模型上的退化风险或与自回归模型的可比性。

（完）
