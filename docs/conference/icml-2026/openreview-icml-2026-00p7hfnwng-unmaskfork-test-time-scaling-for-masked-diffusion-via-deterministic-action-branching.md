---
title: "UnMaskFork: Test-Time Scaling for Masked Diffusion via Deterministic Action Branching"
title_zh: UnMaskFork：通过确定性动作分支实现掩蔽扩散的测试时扩展
authors: "Kou Misaki, Takuya Akiba"
date: 2026-04-30
pdf: "https://openreview.net/pdf/87f4e32340c1d4103c167febbaec24af5eceaa26.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 掩蔽扩散语言模型；测试时扩展；蒙特卡洛树搜索
tldr: 掩蔽扩散语言模型迭代生成的特点天然适合搜索策略。本文提出UnMaskFork框架，将掩蔽轨迹建模为搜索树，使用蒙特卡洛树搜索优化生成路径，通过确定性部分掩蔽动作探索空间。在推理任务上超越随机采样方法。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 自回归模型利用测试时计算增强推理，掩蔽扩散模型尚未充分利用搜索。
method: 将掩蔽扩散过程形式化为搜索树，采用MCTS选择最优掩蔽序列。
result: 在推理基准上，UnMaskFork显著提升掩蔽扩散模型的生成质量。
conclusion: 掩蔽扩散模型通过搜索可获得更强的测试时扩展能力。
---

## Abstract
Test-time scaling strategies have effectively leveraged inference-time compute to enhance the reasoning abilities of Autoregressive Large Language Models. In this work, we demonstrate that Masked Diffusion Language Models (MDLMs) are inherently amenable to advanced search strategies, owing to their iterative and non-autoregressive generation process. To leverage this, we propose **UnMaskFork** (**UMF**), a framework that formulates the unmasking trajectory as a search tree and employs Monte Carlo Tree Search to optimize the generation path. In contrast to standard scaling methods relying on stochastic sampling, UMF explores the search space through deterministic partial unmasking actions performed by multiple MDLMs. Our empirical evaluation demonstrates that UMF consistently outperforms existing test-time scaling baselines on complex coding benchmarks, while also exhibiting strong scalability on mathematical reasoning tasks.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）
- **背景**：自回归大型语言模型已通过测试时计算扩展（test-time scaling）有效提升了推理能力，例如通过搜索或多次采样。然而，掩蔽扩散语言模型（MDLMs）虽然具有迭代、非自回归的生成特性，天然适合搜索策略，但尚未被充分利用进行测试时扩展。
- **问题**：如何利用掩蔽扩散模型的迭代生成过程，设计有效的搜索策略来优化生成路径，从而在推理任务上超越简单的随机采样方法。
- **整体含义**：本文首次将蒙特卡洛树搜索（MCTS）应用于掩蔽扩散模型，提出UnMaskFork框架，为扩散模型在推理任务上的测试时扩展提供了新范式。

## 2. 方法论：核心思想、关键技术细节、算法流程
- **核心思想**：将掩蔽扩散模型的“去掩蔽轨迹”（unmasking trajectory）建模为搜索树，每个节点代表部分生成的序列，动作（action）为确定性部分掩蔽操作（deterministic partial unmasking），通过MCTS选择最优路径。
- **关键技术细节**：
  - 采用多个MDLM实例并行执行确定性部分掩蔽动作，探索搜索空间。
  - MCTS用于平衡探索与利用，评估不同掩蔽路径的预期回报，从而逐步优化生成序列。
  - 与随机采样不同，UnMaskFork利用确定性动作分支，使搜索更具方向性。
- **算法流程**（文字描述）：
  1. 初始化搜索树，根节点为全掩蔽状态。
  2. 每次迭代：选择（Selection）一个叶节点；扩展（Expansion）生成多个子节点，每个子节点对应一个确定性部分掩蔽动作；模拟（Simulation）评估该子节点对应的完整生成路径质量；回传（Backpropagation）更新节点价值。
  3. 最终选择最优路径作为生成结果。

## 3. 实验设计：数据集、基准、对比方法
- **数据集/场景**：复杂编码基准（complex coding benchmarks）和数学推理任务（mathematical reasoning tasks）。
- **基准**：未在摘要中列出具体基准名称，但表明在推理任务上与现有测试时扩展基线进行对比。
- **对比方法**：标准测试时扩展方法（依赖于随机采样）作为基线。

## 4. 资源与算力
- **未明确说明**：论文提供的文本中未提及使用的GPU型号、数量、训练时长等硬件资源信息。无法推断具体算力消耗。

## 5. 实验数量与充分性
- **实验数量**：从摘要看，包含编码和数学两类任务，但未提及具体多少组实验、是否包含消融实验等细节。元数据中没有更详细的信息。
- **充分性与公平性**：由于缺乏详细实验设置，难以评估充分性。但论文声称“consistently outperforms existing baselines”，且被ICML-2026接收，说明实验设计应满足顶会标准。但读者需谨慎，未看到具体数据。

## 6. 主要结论与发现
- 掩蔽扩散语言模型天然适合高级搜索策略。
- UnMaskFork在复杂编码和数学推理基准上始终优于现有测试时扩展基线（如随机采样）。
- 掩蔽扩散模型通过搜索可以获得更强的测试时扩展能力。

## 7. 优点
- **方法新颖性**：首次将MCTS系统性地应用于掩蔽扩散模型生成，利用其迭代非自回归特性。
- **搜索效率**：通过确定性部分掩蔽动作而非随机采样，使搜索更具方向性，有望减少无效探索。
- **通用性**：适用于需要多步推理的任务，如编码和数学。
- **结构清晰**：将生成轨迹转化为搜索树，便于应用成熟的树搜索算法。

## 8. 不足与局限
- **实验覆盖有限**：仅在编码和数学两类推理任务上评估，未在更广泛的语言生成任务（如长文本、对话）上验证。
- **计算开销**：MCTS通常需要大量模拟，且使用多个MDLM实例并行，实际部署时可能计算成本高昂，但文中未讨论。
- **偏差风险**：确定性动作可能限制生成多样性，若任务需要创造性或多样性输出可能不利。
- **应用限制**：适用于需要精确推理的任务，对于开放性任务可能不如随机采样灵活。
- **资源信息缺失**：未提供实验硬件配置，难以复现与比较效率。

（完）
