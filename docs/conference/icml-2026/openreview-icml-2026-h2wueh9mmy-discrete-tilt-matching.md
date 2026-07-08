---
title: Discrete Tilt Matching
title_zh: 离散倾斜匹配
authors: "Yuyuan Chen, Shiyi Wang, Peter Potaptchik, Jaeyeon Kim, Michael Samuel Albergo"
date: 2026-04-30
pdf: "https://openreview.net/pdf/3006cec39228aee41119af3c5546fe9d0530459a.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 掩蔽扩散语言模型；离散空间微调
tldr: 掩蔽扩散大语言模型在下游任务微调时面临边缘似然计算难题。本文推导出离散倾斜匹配（DTM）算法，利用掩蔽后验的动态关系避免显式计算，仅需前向传播奖励，并通过自适应方差控制提高稳定性。在迷宫规划任务上验证了有效性。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 将强化学习适配于扩散语言模型时需要计算边缘似然，难以处理。
method: 利用基础模型与奖励倾斜分布之间的掩蔽后验动态关系，导出DTM算法，避免似然计算。
result: 在迷宫规划任务上，DTM稳定训练并取得良好效果。
conclusion: DTM为扩散语言模型微调提供了一种可计算且稳定的替代方案。
---

## Abstract
Masked diffusion large language models (dLLMs) are a promising alternative to autoregressive generation. While reinforcement learning (RL) algorithms have been adapted to be compatible with dLLMs for fine-tuning them, their reliance on the computation of the marginal likelihood to evaluate policy objectives is intractable. To overcome this, we exploit a dynamical relation between the unmasking posterior of the base model and that which targets the reward-tilted distribution to derive Discrete Tilt Matching (DTM), an algorithm that avoids intractable likelihood evaluation entirely. DTM can be phrased as a cross-entropy loss that only requires forward evaluation of rewards and whose variance can be adaptively controlled, improving training stability. We motivate DTM on maze planning tasks, and show that fine-tuning LLaDA-8B-Instruct with DTM achieves higher accuracy at lower compute costs than prior RL-based fine-tuning methods across the Sudoku, Countdown, and MATH500 benchmarks.

---

## 论文详细总结（自动生成）

# 论文《Discrete Tilt Matching》中文详细总结

## 1. 核心问题与整体含义（研究动机和背景）
- **研究动机**：掩蔽扩散大语言模型（dLLMs）作为自回归生成的有前景替代方案，在微调下游任务时需要将强化学习（RL）算法适配到这类模型上。然而，RL 策略目标的评估依赖于**边缘似然（marginal likelihood）** 的计算，这在扩散语言模型中计算复杂度过高，实际上不可解。
- **整体含义**：本文旨在为扩散语言模型提供一种**无需显式计算边缘似然**的微调方法，从而克服 RL 适配中的根本障碍，使 dLLMs 的微调变得可行且稳定。

## 2. 方法论核心：离散倾斜匹配（Discrete Tilt Matching, DTM）
- **核心思想**：利用**基础模型（base model）的掩蔽后验（unmasking posterior）** 与**奖励倾斜分布（reward-tilted distribution）的掩蔽后验**之间的**动态关系**，直接推导出训练目标，完全避免对边缘似然的显式计算。
- **关键技术细节**：
  - DTM 可被表示为**交叉熵损失（cross-entropy loss）**，该损失仅需对奖励进行**前向传播评估**，无需反向传播通过似然项。
  - 通过**自适应方差控制（adaptive variance control）** 机制，在训练过程中动态调整损失方差，提升训练稳定性。
- **算法流程（文字说明）**：
  1. 从基础掩蔽扩散模型中采样一个带掩蔽的序列路径。
  2. 对当前时间步的掩蔽状态，计算基础模型预测的未掩蔽后验概率。
  3. 通过奖励函数对当前生成样本评分，构造目标分布（奖励倾斜分布）的近似后验。
  4. 利用两个后验之间的 KL 散度或交叉熵作为损失函数，仅依赖奖励信号，无需边缘似然。
  5. 在训练过程中监控损失方差，并动态调整（如通过重要性采样或温度缩放）以保持稳定。

## 3. 实验设计
- **数据集 / 场景**：
  - **迷宫规划任务（maze planning）**：用于初步验证 DTM 的可行性。
  - **三个标准 Benchmark**：**Sudoku**、**Countdown**、**MATH500**。
- **基线方法**：对比了**先前基于 RL 的微调方法**（具体名称未在摘要中详列，但提及“required marginal likelihood”的方法）。
- **模型基础**：微调对象为 **LLaDA-8B-Instruct**。
- **评价指标**：准确率（accuracy）和计算成本（compute cost）。

## 4. 资源与算力
- **文中未明确说明** GPU 型号、数量、训练时长等具体算力信息。仅提及 DTM 在达到相同或更高准确率时**计算成本更低**，但未给出详细数字。

## 5. 实验数量与充分性
- **实验数量**：至少包括 1 个验证性任务（迷宫规划）+ 3 个标准 Benchmark（Sudoku、Countdown、MATH500），共 4 组实验。
- **充分性与公平性**：
  - 基准覆盖了逻辑推理、数学问题、规划等不同类型，具有一定 **多样性**。
  - 对比了先前基于 RL 的微调方法，属于 **同类方法对比**，公平性较好。
  - 但未提及 **消融实验**（如方差控制的必要性、不同损失形式的对比），也未在不同模型尺寸（如 7B、13B）上重复验证，**充分性略显不足**。

## 6. 主要结论与发现
- DTM 成功避免了边缘似然计算，将微调目标简化为仅需前向奖励评估的交叉熵损失。
- 在 LLaDA-8B-Instruct 上，DTM 在 Sudoku、Countdown、MATH500 上 **获得更高准确率**，同时 **计算成本低于** 先前基于 RL 的方法。
- **训练稳定性** 通过自适应方差控制得到改善，这是 DTM 的关键优势。

## 7. 优点
- **计算高效**：完全避免边缘似然，仅需前向传播奖励，大幅降低计算开销。
- **稳定性好**：自适应方差控制机制有效抑制训练波动。
- **方法简洁**：损失函数可表达为交叉熵，易于实现和集成。
- **有前景的应用**：为扩散语言模型微调提供了一种可行的 **替代 RL 范式**。

## 8. 不足与局限
- **实验覆盖有限**：仅在一个中型模型（8B）和三个任务上验证，未在更大模型（如 70B）或更多领域（如文本生成、对话）上测试，**泛化性尚不明确**。
- **缺少消融与敏感性分析**：未报告自适应方差控制的具体影响，也未对比不同损失变体。
- **资源细节缺失**：无具体算力、训练时间、超参数设置，难以复现或评估实际成本。
- **潜在偏差风险**：奖励函数设计未讨论，可能存在 reward hacking 风险；仅对单一 diffuser 架构（LLaDA）验证，对其它扩散语言模型（如 D3PM、MDLM）的适用性未知。
- **应用限制**：当前仅用于离散序列（如规划路径、数字组合），在连续或混合模态上的扩展性未讨论。

（完）
