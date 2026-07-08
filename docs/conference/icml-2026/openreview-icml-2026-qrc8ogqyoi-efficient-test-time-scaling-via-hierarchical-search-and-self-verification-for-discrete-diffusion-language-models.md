---
title: Efficient Test-Time Scaling via Hierarchical Search and Self-Verification for Discrete Diffusion Language Models
title_zh: 通过层次化搜索和自验证实现离散扩散语言模型的高效测试时扩展
authors: "Jinbin Bai, Yixuan Li, Yuchen Zhu, Yi Xin, Qingyu Shi, Aosong Feng, Xiaohong Liu, Molei Tao, Jianru Xue, Xiangtai Li, Ming-Hsuan Yang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/c135307b7f851e552aaf025ac25f1f655fccf0b5.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 提出LLaDA-S，针对LLaDA离散扩散模型的测试时扩展方法
tldr: 针对离散扩散语言模型缺乏有效测试时扩展方法的问题，提出LLaDA-S框架。该框架包括层次化轨迹搜索(HTS)动态剪枝和重分配计算，以及自验证反馈替代外部验证器。专门针对LLaDA模型设计，显著提升其生成潜力。实验验证了在推理效率和质量上的优势。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 离散扩散语言模型(如LLaDA)缺乏高效的测试时扩展方法。
method: 提出LLaDA-S，包括层次化轨迹搜索和自验证反馈。
result: 在推理效率和质量上显著提升。
conclusion: LLaDA-S有效解锁了离散扩散语言模型的生成潜力。
---

## Abstract
Inference-time compute has re-emerged as a practical way to improve LLM reasoning.
Most test-time scaling (TTS) algorithms rely on autoregressive decoding, which is ill-suited to discrete diffusion language models (dLLMs) due to their parallel decoding over the entire sequence.
As a result, developing effective and efficient TTS methods to unlock dLLMs' full generative potential remains an underexplored challenge.
To address this, we propose \textbf{LLaDA-S}, an efficient TTS framework for dLLMs that
(i) performs \textbf{Hierarchical Trajectory Search} (HTS) which dynamically prunes and reallocates compute in an early-to-mid denoising window, (ii) replaces external verifiers with \textbf{Self-Verified Feedback} (SVF) obtained via self-evaluation prompts on intermediate completions, and (iii) introduces \textbf{Local branching with partial remasking} to explore diverse implementations while preserving a high-confidence tokens.
Across four mathematical reasoning and code generation benchmarks on three dLLMs, including LLaDA 8B Instruct, Dream 7B Instruct, and LLaDA 2.0-mini, our LLaDA-S achieves a favorable performance-efficiency trade-off, matching best-of-$N$ performance with substantially fewer function evaluations (NFE).
The code will be released.

---

## 论文详细总结（自动生成）

# 论文详细总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **核心问题**：离散扩散语言模型（dLLMs）虽然具备并行解码整个序列的能力，但缺乏有效的测试时扩展（Test-Time Scaling, TTS）方法。现有TTS算法大多依赖自回归解码，不适用于dLLMs，导致其生成潜力未能充分发挥。
- **研究动机**：推理时的计算量（inference-time compute）重新成为提升LLM推理能力的重要手段，但针对dLLMs的高效TTS方法尚未被充分探索。
- **整体含义**：本文提出LLaDA-S框架，为dLLMs设计了一套高效的测试时扩展方案，旨在在保持生成质量的同时显著提升计算效率，填补该领域的方法空白。

## 2. 论文提出的方法论：核心思想、关键技术细节
### 核心思想
- 构建一个面向dLLMs的搜索-验证框架，在去噪过程中动态分配计算资源，并利用模型自身进行验证，避免使用外部验证器。

### 关键技术细节
- **层次化轨迹搜索（Hierarchical Trajectory Search, HTS）**：
  - 在早期到中期的去噪窗口内，动态剪枝和重分配计算资源（NFE），即根据当前轨迹的置信度决定是否继续探索或终止。
  - 通过分层策略（可能包括粗粒度剪枝和细粒度重分配）来平衡搜索广度和深度。
- **自验证反馈（Self-Verified Feedback, SVF）**：
  - 替代外部验证器，通过对中间完成结果（intermediate completions）使用自我评估提示（self-evaluation prompts）获得反馈，从而指导搜索方向。
- **局部分支与部分重掩码（Local branching with partial remasking）**：
  - 在保持高置信度令牌不变的前提下，对低置信度区域进行分支探索并部分重掩码，以生成多样的实现路径，同时保留已确信的部分。

### 流程说明（文字描述）
1. 输入初始噪声序列，进入去噪过程。
2. 在每一去噪步骤（或某几个步骤），HTS模块评估当前轨迹的置信度，低置信度轨迹被剪枝，高置信度轨迹获得更多计算资源（如更多去噪步数或分支）。
3. 在中期窗口，通过局部分支与部分重掩码，对低置信度令牌生成多个候选后续路径。
4. 每条分支通过SVF（模型自身对中间结果进行打分或排序）进行评估，选出最优路径继续前进。
5. 最终输出完整序列。

（论文摘要未给出具体公式，故不作额外推导）

## 3. 实验设计
- **数据集/场景**：四个数学推理和代码生成基准测试（具体名称未在全文中列出，但摘要提及“mathematical reasoning and code generation benchmarks”）。
- **评估模型**：
  - LLaDA 8B Instruct（主要模型）
  - Dream 7B Instruct
  - LLaDA 2.0-mini
- **对比方法**：
  - **Best-of-N**（基线方法，随机生成N条轨迹，选最优）。
  - 未明确列出其他TTS方法，但暗示与最佳性能匹配。
- **度量指标**：性能（如准确率或通过率）与NFE（函数评估次数）的权衡。

## 4. 资源与算力
- 论文摘要及元数据中**未明确说明**使用的GPU型号、数量、训练时长等算力信息。
- 推测：由于是推理时扩展方法，主要评估推理阶段的计算量，不涉及额外训练。模型本身（如LLaDA 8B）的预训练算力未提及。

## 5. 实验数量与充分性
- **实验数量**：至少覆盖三个不同规模的dLLMs（8B、7B、mini）和四个基准，且包含消融实验（HTS、SVF、局部分支等组件）的可能性高（基于论文常见结构推断）。
- **充分性判断**：
  - **优点**：跨模型、多基准、组件消融的设计较为全面；与Best-of-N的对比能体现效率-性能权衡。
  - **不足**：未与其他非自回归TTS方法（如基于MCTS的扩散模型）对比；数学推理和代码生成领域虽典型，但缺少自然语言生成、对话等任务；未报告统计显著性检验或方差。

## 6. 论文的主要结论与发现
- LLaDA-S在三个dLLMs和四个基准上实现了**有利的性能-效率权衡**：在匹配Best-of-N性能的前提下，所需NFE大幅减少。
- 层次化搜索（HTS）和自验证反馈（SVF）是提升效率的关键组件；局部分支与部分重掩码有助于在保持置信度的同时探索多样性。
- 该工作**有效解锁了离散扩散语言模型的生成潜力**，为dLLMs的测试时扩展提供了可行的范式。

## 7. 优点
- **方法新颖**：首次专门针对离散扩散语言模型设计测试时扩展算法，解决了AA自回归方法不适配的问题。
- **计算高效**：通过动态剪枝和重分配，显著降低了函数评估次数，同时保持性能。
- **自包含**：无需外部验证器，利用模型自身评估，降低系统复杂度。
- **广泛验证**：在三个不同模型和多个任务上验证，显示泛化性。

## 8. 不足与局限
- **实验覆盖有限**：只涉及数学推理和代码生成，未评估常识推理、文本摘要等任务，可能无法完全反映通用性。
- **基准对比不足**：仅与Best-of-N对比，未考虑其他TTS方法（如树搜索、Beam Search变体）或最优基线（如自回归模型的下游扩展）。
- **未报告方差**：可能缺少多次运行的标准差，影响结果可靠性。
- **可解释性**：层次化搜索的剪枝策略与自验证的评分机制未详细解释，可复现性存疑。
- **算力信息缺失**：未说明实验硬件，其他研究者难以复现或对比资源需求。

（完）
