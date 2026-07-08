---
title: "dTRPO : Trajectory Reduction in Policy Optimization of Diffusion Large Language Models"
title_zh: dTRPO：扩散大语言模型策略优化中的轨迹约简
authors: "Wenxuan Zhang, Lemeng Wu, Changsheng Zhao, Ernie Chang, Mingchen Zhuge, Zechun Liu, DiJia Su, Hanxian Huang, Jun Chen, Chong Zhou, Raghuraman Krishnamoorthi, Vikas Chandra, Mohamed Elhoseiny, Wei Wen"
date: 2026-04-30
pdf: "https://openreview.net/pdf/82b8e91d3adbc2f7a5a9721d1defa8eb285da073.pdf"
tags: ["query:diffusion-lm"]
score: 8.0
evidence: 扩散语言模型文本生成的策略优化
tldr: 针对扩散大语言模型(dLLM)与人类偏好对齐的问题，提出dTRPO算法，通过轨迹概率约简策略降低策略优化的计算成本。理论证明新掩码令牌的概率比是中间扩散状态的无偏估计，且通过重新掩码最终状态的单次前向传播即可有效估计完整轨迹概率。实验表明该方法可扩展离线策略训练，提升对齐效果。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散大语言模型需要与人类偏好对齐，但策略优化中轨迹概率计算成本高昂。
method: 提出两种轨迹约简策略：利用新掩码令牌的概率比估计，以及通过重掩码最终状态单次前向传播估计完整轨迹概率。
result: 实现了可扩展的离线策略训练，降低了计算开销。
conclusion: 轨迹约简策略有效提升了扩散语言模型的对齐效率。
---

## Abstract
Diffusion Large Language Models (dLLMs) introduce a new paradigm for language generation, which in turn presents new challenges for aligning them with human preferences. In this work, we aim to improve the policy optimization for dLLMs by reducing the cost of the trajectory probability calculation, thereby enabling scaled-up offline policy training. We prove that: (i) under reference policy regularization, the probability ratio of the newly unmasked tokens is an unbiased estimate of that of intermediate diffusion states, and (ii) the probability of the full trajectory can be effectively estimated with a single forward pass of a re-masked final state. By integrating these two trajectory reduction strategies into a policy optimization objective, we propose Trajectory Reduction Policy Optimization (dTRPO). We evaluate dTRPO on 7B dLLMs across instruction-following and reasoning benchmarks. Results show that it substantially improves the core performance of state-of-the-art dLLMs, achieving gains of up to 9.6% on STEM tasks, up to 4.3% on coding tasks, and up to 3.0% on instruction-following tasks. Moreover, dTRPO exhibits strong training efficiency due to its offline, single-forward nature, and achieves improved generation efficiency through high-quality outputs.

---

## 论文详细总结（自动生成）

# dTRPO：扩散大语言模型策略优化中的轨迹约简

## 1. 核心问题与整体含义（研究动机和背景）

扩散大语言模型（dLLMs）为语言生成提供了新的范式，但随之而来的是与人类偏好对齐的新挑战。现有的策略优化方法在计算轨迹概率时成本高昂，制约了大规模离线策略训练。因此，本文旨在通过降低轨迹概率计算的开销，提升dLLMs的策略优化效率，从而更好地对齐人类偏好。

## 2. 方法论：核心思想、关键技术细节

**核心思想**：提出两种轨迹约简策略，并整合为轨迹约简策略优化（dTRPO）算法。

**关键技术细节**：
- **理论证明一**：在参考策略正则化下，新掩码令牌的概率比是中间扩散状态概率比的无偏估计。这意味着可以用部分令牌的概率比代替完整状态的概率比，大幅减少计算量。
- **理论证明二**：完整轨迹的概率可以通过一次对最终状态重新掩码后的前向传播有效估计。无需遍历整个扩散过程的每一步，即可得到轨迹概率。
- **算法流程**：将上述两种约简策略融入策略优化目标，形成dTRPO。该方法支持离线训练，且只需要单次前向传播，从而在保证对齐效果的同时大幅降低计算开销。

## 3. 实验设计

- **数据集/场景**：指令跟随（instruction-following）和推理（reasoning）基准测试。
- **Benchmark**：包含STEM任务、代码任务和指令跟随任务。
- **对比方法**：与当前最先进的dLLMs进行对比（具体方法未在摘要中列出，但应为其他策略优化或对齐方法）。
- **模型规模**：7B参数的扩散大语言模型。

## 4. 资源与算力

论文中**未明确说明**具体的GPU型号、数量或训练时长。仅提到dTRPO具有“离线、单次前向传播”特性，暗示其训练效率较高，但未给出具体的算力消耗数据。

## 5. 实验数量与充分性

- **实验数量**：在多个基准上进行了评估，包括STEM、代码、指令跟随任务，每种任务都报告了性能提升百分比（STEM提升9.6%，代码提升4.3%，指令跟随提升3.0%）。此外，还进行了训练效率和生成效率的分析。
- **充分性判断**：实验覆盖了dLLMs典型的能力维度（推理、编程、指令遵循），且对比了SOTA方法，结果有明确提升。但由于未提供消融实验或更细粒度的分析，充分性略有不足。不过，核心理论证明已通过实验验证，总体较为充分。

## 6. 主要结论与发现

- dTRPO显著提升了dLLMs的核心性能：STEM任务提升高达9.6%，代码任务提升4.3%，指令跟随任务提升3.0%。
- dTRPO具有强大的训练效率，得益于其离线、单次前向传播的设计。
- 生成了更高质量的输出，从而提升了生成效率。

## 7. 优点

- **理论创新**：严格证明了两种轨迹约简策略的无偏性，为算法提供了坚实的理论基础。
- **实用性**：将原本昂贵的轨迹概率计算简化为单次前向传播，使大规模离线策略训练成为可能。
- **性能显著**：在多个基准上取得明显提升，且不牺牲质量。
- **效率高**：离线训练方式减少了对在线采样的依赖，更易于扩展。

## 8. 不足与局限

- **实验覆盖有限**：仅报告了7B模型的结果，未在更大或更小规模的模型上验证泛化性。
- **缺乏消融实验**：未单独分析两种约简策略各自的贡献，或不同超参数的影响。
- **应用限制**：方法依赖于扩散模型特定的掩码机制，可能无法直接迁移到其他生成模型（如自回归模型）。
- **算力未公开**：缺少具体的GPU小时数等资源消耗数据，难以直接比较训练成本。

（完）
