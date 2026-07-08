---
title: "CORE: Context-Robust Remasking for Diffusion Language Models"
title_zh: CORE：面向扩散语言模型的上下文鲁棒重掩码
authors: "Kevin Zhai, Sabbir Mollah, Zhenyi Wang, Mubarak Shah"
date: 2026-04-30
pdf: "https://openreview.net/pdf/0629c6eeded945e6bf2f33b99ab6495bc72f0efa.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 通过上下文鲁棒重掩码改进掩码扩散模型的解码
tldr: 掩码扩散模型的标准解码存在上下文刚性：早期预测的token即使置信度高也可能缺乏全局上下文，导致级联错误。现有修订策略依赖静态置信度，但矛盾的是，不一致的token往往也表现出高置信度。本文提出CORE，一种无需训练的重掩码框架，通过对抗性探测识别上下文脆弱token，并重新掩码它们，从而避免级联错误。在多个文本生成任务上，CORE显著提升了生成质量，且不增加训练开销。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有掩码扩散解码中，早期高置信度token可能因缺乏全局上下文而导致级联错误且难以纠正。
method: 提出CORE框架，通过对抗性探测识别上下文脆弱token，并在推理过程中重新掩码这些token。
result: CORE在多个文本生成基准上提升了生成质量，且无需额外训练。
conclusion: 上下文鲁棒重掩码是一种有效的无训练推理时改进策略，可增强扩散语言模型的生成稳定性。
---

## Abstract
Standard decoding in Masked Diffusion Models (MDMs) is hindered by context rigidity: tokens are retained based on transient high confidence, often ignoring that early predictions lack full context. This creates cascade effects where initial inconsistencies misguide the remaining generation. Existing revision strategies attempt to mitigate this by relying on static confidence scores, but these signals are inherently myopic; inconsistent tokens frequently appear confident to the model itself. To address this, we propose Context-Robust Remasking (CORE), a training-free framework for inference-time revision. We introduce a new selection paradigm: rather than trusting static token probabilities, we identify *context-brittle* tokens by probing their sensitivity to adversarial perturbations. We formalize revision as a robust optimization problem targeting worst-case context shifts. CORE efficiently approximates this objective using theoretically bounded probability margins to expose and revise unstable tokens. On LLaDA-8B-Base, CORE delivers consistent improvements across reasoning and code benchmarks, outperforming compute-matched baselines and boosting performance on code generation (MBPP) by up to $+9.2\\%$, with comparable gains generalizing to the Dream-7B architecture.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）
掩码扩散模型（Masked Diffusion Models, MDMs）在标准解码过程中存在 **上下文刚性（context rigidity）** 问题：模型会基于瞬时的置信度高值保留 token，但这些早期预测往往缺乏全局上下文，导致级联错误——早期的不一致 token 错误地引导后续生成。虽然已有修订策略尝试使用静态置信度分数来修正，但矛盾的是，不一致的 token 在模型自身看来往往也表现出高置信度，因此静态置信度信号具有 **固有短视性**。此问题限制了扩散语言模型的生成质量与稳定性。

## 2. 方法论：核心思想、关键技术细节、算法流程
**CORE（Context-Robust Remasking）** 是一个 **无需训练** 的推理时重掩码框架，用于修订已生成的 token。

- **核心思想**：不再信任静态的 token 概率，而是通过探测 token 对 **对抗性扰动** 的敏感性来识别 **上下文脆弱（context-brittle）** 的 token。
- **形式化**：将修订问题建模为 **鲁棒优化问题**，目标是最坏情况下的上下文偏移。
- **实现**：CORE 使用 **理论上有界的概率边际（probability margins）** 来高效近似该目标，从而暴露并修订不稳定的 token。
- **工作流程**（文字描述）：
  1. 在解码过程中，对当前已生成的部分施加微小对抗性扰动（如输入上下文噪声）。
  2. 观察每个 token 概率的波动幅度（即概率边际），波动大的 token 被判定为“上下文脆弱”。
  3. 将这些脆弱 token **重新掩码**，让模型在更全局的上下文中重新生成，从而避免级联错误。

该方法无需额外训练，直接作用于已训练好的扩散语言模型。

## 3. 实验设计
- **基准模型**：主要测试于 **LLaDA-8B-Base** 架构，并泛化至 **Dream-7B** 架构。
- **任务与数据集**：涉及 **推理** 和 **代码生成** 基准。具体数据集包括 **MBPP**（代码生成）。
- **对比方法**：与 **计算量匹配的基线（compute-matched baselines）** 比较。未明确列出基线名称，但推测包括标准 MDM 解码及其他现有修订策略。
- **评估指标**：在 MBPP 上报告了提升百分比（+9.2%），其他任务也有类似增益。

**注意**：论文摘要未提供更多数据集细节（如推理任务的名称、消融实验数量等），因此实验覆盖的广度与深度需依赖原文补充。

## 4. 资源与算力
论文摘要及元数据中 **未明确说明** 所使用的 GPU 型号、数量、训练时长或推理开销。仅提及 CORE 是“无需训练”的推理时框架，因此主要算力消耗在推理阶段。具体资源未见披露。

## 5. 实验数量与充分性
- **实验数量**：仅从摘要中得知在 LLaDA-8B-Base 上进行了推理和代码生成测试，并在 Dream-7B 上进行了泛化验证。提及“多个文本生成基准”，但未列出具体数量。
- **充分性评价**：
  - **优点**：覆盖不同架构（8B 和 7B），且结果显著（+9.2%），具有说服力。
  - **不足**：缺乏消融实验、超参数敏感性分析、与更多基线（如大型语言模型的对比）的详细对比，也未提供统计显著性检验。因此 **实验充分性一般**，建议补充更多元的数据集和消融研究。

## 6. 主要结论与发现
- CORE 在 **不增加训练开销** 的前提下，显著提升了扩散语言模型的生成质量。
- 在代码生成（MBPP）上，CORE 相比计算量匹配的基线提升了 **+9.2%**。
- 该方法具有良好的架构泛化能力，在 Dream-7B 上也观察到可比增益。
- 核心发现：**上下文脆弱 token 的对抗性探测** 比 **静态置信度** 更可靠，是解决级联错误的有效策略。

## 7. 优点
1. **无需训练**：直接应用于预训练模型，节省训练成本和数据需求。
2. **理论保证**：利用有界概率边际近似鲁棒优化，方法可解释且高效。
3. **显著提升**：在代码生成任务上获得大幅提升，且推理成本可控。
4. **泛化性好**：在两种不同架构上均有效，表明方法不是特定模型的特例。
5. **问题诊断新颖**：首次提出“上下文脆弱性”概念，并引入对抗性探测进行识别，思路创新。

## 8. 不足与局限
1. **实验覆盖有限**：仅公开了 MBPP 一个数据集的详细提升，未报告更多文本生成任务（如机器翻译、摘要）及更大规模模型的测试。
2. **消融分析缺失**：未分析不同对抗扰动强度、概率边际阈值等超参数的影响，也未与动态置信度修正式基线比较。
3. **计算开销未量化**：尽管是推理时方法，但对抗性探测会增加额外推理计算量，文中未量化时间或显存增加。
4. **可能偏差**：仅在 8B 和 7B 规模的模型上测试，对更小或更大的扩散语言模型效果未经验证。
5. **应用限制**：当前仅针对掩码扩散模型，是否可推广至其他扩散范式（如连续扩散 LM）或自回归模型尚不明确。

（完）
