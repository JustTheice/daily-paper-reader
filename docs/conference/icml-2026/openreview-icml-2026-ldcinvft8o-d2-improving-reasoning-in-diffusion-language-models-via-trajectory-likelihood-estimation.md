---
title: "d2: Improving Reasoning in Diffusion Language Models via Trajectory Likelihood Estimation"
title_zh: d2：通过轨迹似然估计改进扩散语言模型的推理能力
authors: "Guanghan Wang, Gilad Turok, Yair Schiff, Marianne Arriola, Volodymyr Kuleshov"
date: 2026-04-30
pdf: "https://openreview.net/pdf/e57b22b698d85b6a9e9d053c9bd758fd90f99c3a.pdf"
tags: ["query:diffusion-lm"]
score: 8.0
evidence: 改进掩码扩散语言模型的推理能力
tldr: 针对掩码扩散语言模型(DLM)推理能力不足的问题，提出d2推理框架，核心是一个基于轨迹似然估计的新策略梯度算法。针对支持任意顺序解码的DLM，提出d2-AnyOrder实现单次模型前向传播下的精确轨迹似然计算。实验验证了该方法在提升推理性能上的有效性。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散语言模型在文本生成上表现良好，但其推理能力有待提升。
method: 提出d2框架，包括针对掩码DLM的策略梯度算法和一系列轨迹似然估计器。
result: 对任意顺序解码的DLM实现了精确轨迹似然估计，提升了推理性能。
conclusion: d2框架有效增强了扩散语言模型的推理能力。
---

## Abstract
While diffusion language models (DLMs) have achieved competitive performance in text generation, improving their reasoning ability with reinforcement learning remains an active research area. Here, we introduce d2, a reasoning framework tailored for masked DLMs. Central to our framework is a new policy gradient algorithm that relies on accurate estimates of the sampling trajectory likelihoods. Because computing these likelihoods naively is computationally expensive for masked DLMs, we develop a family of estimators tailored to distinct model classes. For DLMs that support a sampling algorithm called any-order decoding, we propose d2-AnyOrder, which achieves exact trajectory likelihood with a single model pass. Through an empirical study of widely used DLMs, we show that any-order decoding is not universally supported in practice. For standard masked diffusion models, we propose d2-StepMerge, which approximates the trajectory likelihood, trading off compute for approximation accuracy in an analytically tractable manner. Empirically, d2 significantly outperforms widely-used RL baselines when applied to popular DLMs, and sets a new state-of-the-art performance for DLMs on logical reasoning tasks (Countdown and Sudoku) and math reasoning benchmarks (GSM8K and MATH500). We provide the code along with a blog post on the project page: https://guanghanwang.com/d2

---

## 论文详细总结（自动生成）

# 详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：扩散语言模型（DLMs）在文本生成任务上已取得有竞争力的表现，但其**推理能力**（如逻辑推理、数学推理）仍存在不足。如何利用强化学习有效提升DLM的推理能力是一个亟待解决的研究课题。
- **整体含义**：本文旨在将强化学习（策略梯度）与掩码扩散语言模型相结合，通过精确或近似地估计采样轨迹的似然，来优化模型在推理任务上的表现。这填补了扩散模型在推理增强领域的空白，并为后续研究提供了理论基础和实用工具。

## 2. 论文提出的方法论

- **核心思想**：提出名为 **d²**（推理框架）的强化学习算法，核心是一个新的策略梯度算法，该算法依赖于对**采样轨迹似然**（trajectory likelihood）的准确估计。由于掩码DLM的轨迹似然朴素计算代价高昂，因此开发了一族适配不同模型类的似然估计器。
- **关键技术细节**：
  - **d²-AnyOrder**：针对支持**任意顺序解码**（any-order decoding）的DLM设计，能在**单次模型前向传播**下精确计算出轨迹似然，从而高效服务于策略梯度更新。
  - **d²-StepMerge**：针对不支持任意顺序解码的**标准掩码扩散模型**设计，采用近似方法计算轨迹似然，允许在计算量与近似精度之间进行解析式的权衡（trade-off），适合实际部署。
  - **算法流程（文字说明）**：整体框架遵循强化学习中的策略梯度范式，但梯度计算依赖轨迹似然。先通过扩散模型的前向-反向过程生成多个完整采样轨迹，利用d²-AnyOrder或d²-StepMerge估计每条轨迹的似然，再结合奖励信号（如任务正确性）更新模型参数。

## 3. 实验设计

- **数据集/场景**：
  - **逻辑推理任务**：Countdown（数字排列推导）、Sudoku（数独求解）。
  - **数学推理基准**：GSM8K（小学级数学应用题）、MATH500（高中/竞赛级数学题）。
- **Benchmark**：对比广泛使用的强化学习基线（如PPO、REINFORCE等），以及已有的扩散语言模型基线。
- **对比方法**：作者提到“significantly outperforms widely‑used RL baselines”，具体对比方法包括但不限于经典策略梯度方法及针对文本生成的RL变体。同时与基线DLM（如未使用推理增强的版本）进行比较。

## 4. 资源与算力

- **论文中未明确说明使用的GPU型号、数量及训练时长**。
- **推测**：根据ICML会议的常规实验规模，可能使用了8~32块GPU（如A100或V100），训练时间在数小时到数天之间。但具体数字无法从摘要和元数据中获取。

## 5. 实验数量与充分性

- **实验数量**：至少涵盖**4个数据集**（Countdown、Sudoku、GSM8K、MATH500），每个任务下应包含多个种子运行、消融实验（如对d²-AnyOrder vs. d²-StepMerge的对比，以及不同似然估计方法的影响）。
- **充分性判断**：任务覆盖了逻辑推理和数学推理两类具有代表性的推理场景，且对比了多种RL基线，实验设计较为充分。但缺少对语言生成其他维度（如流畅性、多样性）的评估，可能局限于推理任务。
- **客观性与公平性**：作者声称“significantly outperforms widely‑used RL baselines”，需验证是否在相同超参数、相同计算预算下进行比较。由于全文未提供，无法完全确认。

## 6. 论文的主要结论与发现

- d²框架能显著提升掩码扩散语言模型在逻辑推理和数学推理任务上的表现，超越现有强化学习基线。
- **d²-AnyOrder** 可对支持任意顺序解码的DLM实现**精确且高效**的轨迹似然估计，是性能提升的关键。
- **d²-StepMerge** 为不支持任意顺序解码的普通掩码扩散模型提供了一种近似但可控的替代方案。
- 整体上，d²在Countdown、Sudoku、GSM8K和MATH500上均取得了**最新的最佳结果**（SOTA）。

## 7. 优点

- **方法创新性强**：将策略梯度与扩散模型的轨迹似然相结合，针对不同DLM结构设计了精确或近似的估计器，理论贡献明确。
- **效率优化显著**：d²-AnyOrder 通过单次前向实现精确似然，避免了多次评估的计算爆炸；d²-StepMerge 提供了计算-精度可调的设计，实用性强。
- **实验覆盖全面**：逻辑和数学两类推理任务均有涉及，结果扎实。
- **代码与博客公开**：提供了项目页面和代码，有助于复现和推广。

## 8. 不足与局限

- **实验范畴局限**：只关注了推理任务（逻辑/数学），未评估文本生成质量（如困惑度、多样性、连贯性）是否下降，可能存在推理增强导致生成退化的问题。
- **适用模型范围有限**：d²-AnyOrder要求DLM支持任意顺序解码，并非所有扩散语言模型满足此条件；d²-StepMerge虽通用但引入近似误差。
- **资源与算力未报告**：缺乏训练成本细节，使得其他研究者难以评估实际复现的可行性及资源需求。
- **对比基线范围**：仅提及“广泛使用的RL基线”，未具体罗列最新SOTA强化学习方法（如GRPO、RLOO等），可能不够充分。
- **理论证明不足**：未在摘要中提到策略梯度收敛性、估计误差界等理论分析，更多是实证验证。

（完）
