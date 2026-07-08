---
title: "Scheduling Thoughts: Learning the Order of Thought in Diffusion Language Models"
title_zh: 调度思维：学习扩散语言模型中的思考顺序
authors: "Jiawei Xu, Minghui Liu, Aakriti Agrawal, Yifan Chen, Furong Huang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/2958518c93de88e5b2779e334cb4e860a8053438.pdf"
tags: ["query:diffusion-lm"]
score: 8.0
evidence: 学习掩码扩散语言模型的解掩码顺序
tldr: 针对掩码扩散语言模型解掩码顺序通常由启发式方法决定而影响生成质量的问题，推导了序列解码失配的可处理上界，将其转化为基于路径似然的密集自感知奖励，并将顺序选择建模为策略优化问题。提出自我感知调度(SAS)学习轻量级的顺序策略。实验表明该方法能有效提升生成质量。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 掩码扩散语言模型的解掩码顺序对生成质量影响大，但通常使用启发式方法。
method: 推导序列解码失配上界，转化为自感知奖励，并通过策略优化学习顺序。
result: 学习到的顺序策略有效提升生成质量。
conclusion: SAS将顺序选择建模为策略优化，实现了更优的解码顺序。
---

## Abstract
Masked diffusion language models decode by iteratively unmasking tokens, where the unmasking order defines an ``order of thought'' 
that strongly influences generation quality yet is typically chosen heuristically. 
We derive a tractable upper bound on the sequential decoding mismatch, measured by the Kullback–Leibler divergence and expressed in terms of the model’s pathwise log-likelihood, with tightness under sufficient model expressivity. 
This bound induces a dense self-aware reward for a target sequence $x$ and unmasking order $\sigma$, 
over ordered paths,
casting order selection as a principled policy optimization problem with a frozen denoiser. 
We instantiate this idea as **Self-Aware Scheduling (SAS)**, which learns a lightweight order policy using Group Relative Policy Optimization and applies seamlessly to both sequential and semi-autoregressive decoding. 
On Sudoku with 1B MDM, SAS improves puzzle accuracy from $82.0\%$ (best heuristic schedule) to $91.8\%$, and reaches $97.9\%$ with second-stage fine-tuning along learned trajectories. 
On LLaDA-8B, SAS improves pass@1 on GSM8K from $64\%$ to $76\%$ (full diffusion) and on MBPP from $39.5\%$ to $41\%$, while consistently matching or exceeding heuristic schedules across generation lengths and block sizes.

---

## 论文详细总结（自动生成）

# 论文《调度思维：学习扩散语言模型中的思考顺序》详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：掩码扩散语言模型（Masked Diffusion Language Models）通过迭代解掩码（unmasking）生成文本，而解掩码的顺序（即“思考顺序”）对生成质量有显著影响。然而，当前实践中通常采用启发式方法（如随机、固定顺序）来确定该顺序，缺乏理论指导，导致生成质量受限。
- **研究动机**：探索能否**学习**一个最优的解掩码顺序，从而提升模型的生成性能，并将该问题转化为一个可优化的策略学习问题。
- **整体含义**：本文从理论上推导了序列解码失配的上界，并基于此设计了一种自感知奖励（self-aware reward），将顺序选择建模为策略优化问题，从而实现了对“思考顺序”的自动化学习，显著提升了生成质量。

## 2. 论文提出的方法论

- **核心思想**：将解掩码顺序的选择视为一个策略优化问题，目标是最小化序列解码的KL散度失配，并将失配上界表达为模型路径似然（pathwise log-likelihood）的函数，从而构造一个密集的自感知奖励来指导顺序策略的学习。
- **关键技术细节**：
  - 推导了**序列解码失配**的可处理上界（使用KL散度度量），该上界在模型表达力足够时紧致。
  - 基于该上界构造了针对目标序列\(x\)和解掩码顺序\(\sigma\)的**自感知奖励**，该奖励逐路径计算，可对每一步的解掩码决策提供反馈。
  - 将顺序选择问题建模为以固定去噪器（frozen denoiser）为基础的**策略优化**问题，并使用**组相对策略优化（GRPO）** 来学习一个轻量级的顺序策略网络。
  - 提出的方法称为**Self-Aware Scheduling (SAS)**，可无缝应用于**顺序解码**和**半自回归解码**两种模式。
- **算法流程（文字说明）**：
  1. 定义掩码扩散模型，其生成过程为迭代解掩码。
  2. 对于给定的目标序列\(x\)，枚举可能的解掩码顺序\(\sigma\)（或采样），计算每条路径的路径似然。
  3. 利用推导的上界计算每个顺序\(\sigma\)的自感知奖励。
  4. 使用GRPO优化一个轻量级策略网络（顺序生成器），使其在给定部分解掩码状态时，选择下一解掩码位置能最大化累积奖励。
  5. 在推理时，用训练好的策略网络替代启发式顺序，引导去噪器生成更优的文本。

## 3. 实验设计

- **使用的数据集与场景**：
  - **Sudoku**（数独）：使用1B参数的掩码扩散模型（1B MDM）进行实验，评估解谜准确性（Puzzle Accuracy）。
  - **GSM8K**（数学推理）：使用LLaDA-8B模型（8B参数扩散模型），评估pass@1准确率。
  - **MBPP**（代码生成）：使用LLaDA-8B模型，评估pass@1准确率。
- **Benchmark与对比方法**：
  - 与**最佳启发式调度**（best heuristic schedule）对比：在Sudoku上启发式最佳准确率为82.0%；在GSM8K上为64%；在MBPP上为39.5%。
  - 还对比了不同生成长度和块大小（block size）下的表现，验证SAS的鲁棒性。
- **消融与扩展**：
  - 在Sudoku上进行了**第二阶段微调**：沿着学习到的轨迹（learned trajectories）对模型进行微调，准确率进一步提升至97.9%。
  - 在GSM8K和MBPP上，除了全扩散（full diffusion）设置，还测试了半自回归解码下的性能，SAS均一致优于或匹配启发式调度。

## 4. 资源与算力

- 论文中**未明确说明**所使用的GPU型号、数量、训练时长等具体算力信息。
- 仅提及使用1B MDM和LLaDA-8B模型进行实验，但未披露训练或推理的硬件环境。
- **需要指出**：缺乏算力资源描述，可能影响实验的可复现性和对计算成本的理解。

## 5. 实验数量与充分性

- **实验数量**：
  - 主要三个任务（Sudoku、GSM8K、MBPP）各进行了主实验。
  - 在Sudoku上还有第二阶段微调实验。
  - 在GSM8K和MBPP上报告了不同块大小下的结果，以及全扩散和半自回归两种解码模式下的对比。
  - 未明确报告多个随机种子或置信区间，但从“matching or exceeding heuristic schedules across generation lengths and block sizes”可见进行了多组配置的测试。
- **充分性评估**：
  - 实验覆盖了**数值推理、数学问答、代码生成**三类任务，具有一定多样性。
  - 对比了启发式基线，并展示了显著提升，结论可信。
  - **不足**：数据集仅三个，且规模较小（Sudoku为有限状态，GSM8K/MBPP均属常识推理和代码），未覆盖长文本生成、开放域对话等更复杂的自然语言生成场景；缺乏与更先进顺序学习方法的对比（如强化学习基线、搜索方法等）。

## 6. 论文的主要结论与发现

- **主要结论**：SAS方法能够学习到比最佳启发式调度更优的解掩码顺序，显著提升生成质量。
- **具体发现**：
  - 在Sudoku上，SAS将准确率从82.0%提升至91.8%，第二阶段微调后达到97.9%。
  - 在GSM8K上，SAS将pass@1从64%提升至76%（全扩散设置）。
  - 在MBPP上，SAS将pass@1从39.5%提升至41%。
  - SAS在不同生成长度和块大小下表现稳定，始终匹配或超过启发式方法。

## 7. 优点：方法或实验设计上的亮点

- **理论贡献**：推导了序列解码失配的可处理上界，并转化为可操作的奖励函数，为学习解码顺序提供了理论依据。
- **方法优雅**：将顺序选择建模为策略优化问题，无需修改预训练去噪器（frozen denoiser），轻量级策略网络易于训练且通用性强。
- **实用性强**：SAS可应用于顺序解码和半自回归解码两种常见模式，直接替换现有启发式调度，应用成本低。
- **实验证明有效**：在多个模型（1B MDM, LLaDA-8B）和任务上均取得显著提升，且与第二阶段微调兼容，进一步放大收益。

## 8. 不足与局限

- **实验覆盖有限**：仅测试了Sudoku、GSM8K、MBPP三个数据集，缺乏对长文本生成、故事创作、开放域对话等更复杂任务的评估，结果泛化性需更多验证。
- **算力资源未报告**：无法评估方法的训练成本或可复现性，不利于社区复现。
- **潜在假设限制**：推导的上界依赖于“模型表达力足够”的假设，对于表达能力有限的模型，上界可能不紧致，从而影响奖励的准确性。
- **缺乏基线对比多样性**：仅与启发式方法对比，未涉及其他学习型顺序方法（如基于注意力的预测、搜索解码等），说服力可能不够强。
- **偏差风险**：在Sudoku这类确定性任务上提升很大，但在GSM8K/MBPP上提升相对较小（例如MBPP仅+1.5%），说明方法可能对任务类型敏感，在开放生成任务上的收益需进一步探索。

（完）
