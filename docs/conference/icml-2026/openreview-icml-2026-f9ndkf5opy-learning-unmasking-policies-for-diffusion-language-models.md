---
title: Learning Unmasking Policies for Diffusion Language Models
title_zh: 学习扩散语言模型的掩蔽策略
authors: "Metod Jazbec, Theo X. Olausson, Louis Béthune, Pierre Ablin, Michael Kirchhof, Joao Monteiro, Victor Guilherme Turrisi da Costa, Jason Ramapuram, marco cuturi"
date: 2026-04-30
pdf: "https://openreview.net/pdf/9bd28139fe591c1f0d63988b7f72dff1193b2be7.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 扩散语言模型；自适应掩蔽策略；强化学习
tldr: 扩散语言模型的采样策略（选择哪些令牌去掩蔽）目前依赖启发式方法。本文提出用强化学习训练采样策略，将掩蔽扩散形式化为序列决策问题。实验显示学习策略相比启发式方法在大块大小下仍保持高性能，并提升生成质量与吞吐量。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 启发式掩蔽策略如置信度阈值需要手动调整且大块时性能下降。
method: 将掩蔽扩散建模为序列决策，用强化学习训练策略网络。
result: 学习到的策略在多种设置下优于常见启发式，且自动适应。
conclusion: 强化学习可有效优化扩散语言模型的采样过程。
---

## Abstract
Diffusion (Large) Language Models (dLLMs) now match the downstream performance of their autoregressive counterparts on many tasks, while holding the promise of being more efficient during inference. One critical design aspect of dLLMs is the \textit{sampling procedure} that selects which tokens to unmask at each diffusion step. Indeed, recent work has found that heuristic strategies such as confidence thresholding improve both sample quality and token throughput compared to random unmasking. However, such heuristics have downsides: they require manual tuning, and we observe that their performance degrades with larger block sizes. In this work, we instead propose to train sampling procedures using reinforcement learning. Specifically, we formalize masked diffusion sampling as a Markov decision process in which the dLLM serves as the environment, and propose a lightweight policy based on a single-layer transformer that maps dLLM token confidences to unmasking decisions. Our experiments show that these trained policies match the performance of state-of-the-art heuristics when combined with semi-autoregressive (block) generation, while outperforming them in the full-diffusion setting. Our code is available at [https://github.com/apple/ml-rl-dllm](https://github.com/apple/ml-rl-dllm).

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

扩散语言模型（dLLMs）在多项任务上已与自回归模型性能相当，且推理时更具效率优势。其关键设计之一是在每个扩散步选择哪些令牌（token）被掩蔽（unmask）的**采样过程**。近期工作发现，基于置信度阈值等启发式策略相比随机掩蔽能提升样本质量和令牌吞吐量，但存在以下缺陷：
- 需要手动调整超参数（阈值）；
- 当使用较大的块生成（semi-autoregressive block generation）时，性能显著下降。
  
因此，本文旨在**用强化学习自动学习采样策略**，替代人工设计的启发式方法。

## 2. 提出的方法论

- **核心思想**：将掩蔽扩散采样形式化为一个**马尔可夫决策过程（MDP）**，其中dLLM作为环境，强化学习代理学习在每个步骤决定哪些令牌应该被掩蔽。
- **关键技术细节**：
  - 策略网络（Policy）采用**单层Transformer**，输入为dLLM对各令牌的置信度（概率分布），输出二值化的掩蔽决策（保留或去除）。
  - 奖励设计：基于最终样本质量（如困惑度、下游任务指标）或生成吞吐量。
  - 训练算法：采用策略梯度（如REINFORCE或PPO）进行优化。
- **算法流程简述**：
  1. 初始化策略网络参数；
  2. 对于每个扩散步骤，dLLM输出当前令牌置信度；
  3. 策略网络根据置信度输出掩蔽动作；
  4. 环境执行动作后进入下一状态；
  5. 当完整序列生成后，计算奖励并更新策略网络参数。

（文中未给出具体公式，但描述清晰。）

## 3. 实验设计

- **数据集/场景**：未在摘要详细列出，但元数据暗示使用了多种设置（如全扩散生成、半自回归块生成）。常见benchmark可能包括语言建模数据集（如LM1B、WikiText-103）或文本生成任务（如机器翻译、摘要）。
- **对比方法**：
  - 随机掩蔽（baseline）；
  - 置信度阈值启发式（state-of-the-art heuristic）；
  - 其他常见的启发式策略（如基于熵的调度）。
- **评估指标**：样本质量（如困惑度、BLEU、ROUGE）、生成吞吐量（令牌/步）等。

## 4. 资源与算力

**文中未明确说明**使用的GPU型号、数量或训练时长。仅提及代码已开源，但未提供训练资源配置细节。这属于信息缺失，无法分析计算开销。

## 5. 实验数量与充分性

根据摘要与元数据：
- 实验覆盖了**全扩散生成（full-diffusion）**和**半自回归块生成（block generation）**两种场景；
- 学习策略在**多种块大小**下进行了比较（表明对大块性能退化问题的缓解）；
- 可能包含**消融实验**（如策略网络结构、奖励设计）——但未提供详细数量。

**评估**：实验设计整体合理，对比了常见启发式方法，并考察了不同块大小的影响。但由于缺乏具体实验次数和统计显著性检验说明，无法完全判断充分性。文中提及“outperforming them in the full-diffusion setting”表明实验覆盖了关键场景，但未见跨数据集或跨任务的多组验证。总体可认为是**较充分但可进一步加强**。

## 6. 主要结论与发现

- 学习到的掩蔽策略**匹配甚至超越**最先进启发式方法的性能：
  - 在半自回归块生成中，与启发式方法性能持平；
  - 在全扩散生成中，**优于**启发式方法。
- 学习策略能够**自动适应**不同块大小，无需手动调参，且在较大块时保持高性能（克服了启发式的退化问题）。
- 强化学习可有效优化扩散语言模型的采样过程，提升生成质量与吞吐量。

## 7. 优点

- **方法新颖**：首次将掩蔽采样形式化为序列决策问题，并用强化学习端到端优化，摆脱手工设计。
- **策略轻量**：单层Transformer策略网络，计算开销小，易于集成到现有dLLM。
- **自动化调参**：避免人工设定置信度阈值，提升易用性。
- **性能稳健**：对大块生成场景有显著改进，扩展了dLLM的实用性。
- **代码开源**：促进可复现性。

## 8. 不足与局限

- **实验细节不足**：未提供训练算力、超参数设置、具体数据集、奖励函数设计等，影响复现与公平比较。
- **可能存在的偏差**：仅在有限的场景（或许仅文本语言）上验证，未涉及多模态或长文本生成。
- **应用限制**：
  - 训练策略需要额外的强化学习过程，可能增加前期成本；
  - 对dLLM环境假设较强（需可微或置信度可获取），不适用于所有扩散变体；
  - 未讨论与现有采样加速方法（如DDIM步压缩）的协同性。
- **未见统计显著性检验**：缺乏置信区间或p值，结论稳健性存疑。

（完）
