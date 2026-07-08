---
title: "Plan for Speed: Dilated Scheduling for Masked Diffusion Language Models"
title_zh: 规划速度：面向掩码扩散语言模型的空洞调度
authors: "Omer Luxembourg, Haim H. Permuter, Eliya Nachmani"
date: 2026-04-30
pdf: "https://openreview.net/pdf/4ad0a15b7fb9971031d787a219989b8ae37e8fa7.pdf"
tags: ["query:diffusion-lm"]
score: 8.0
evidence: 面向掩码扩散语言模型的空洞调度
tldr: 针对掩码扩散语言模型（MDLM）并行解掩时忽略位置交互导致退化为自回归行为的问题，提出空洞解掩调度器（DUS），通过将序列位置划分为不相邻的空洞组并并行解掩，以最小化每步联合熵增益的上界。在数学和文本生成任务上，DUS在相同网络调用次数下显著优于现有并行解掩策略。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: MDLM并行解掩策略忽略位置交互，有效退化为自回归，速度提升有限。
method: 提出空洞解掩调度（DUS），将序列划分为非相邻组并行解掩，最小化联合熵增益。
result: 在GSM8K等数据集上，DUS以更少的网络调用达到接近自回归顺序解码的质量。
conclusion: 通过考虑位置间交互可显著提升MDLM并行解码的效率与质量。
---

## Abstract
Masked diffusion language models (MDLMs) promise fast, non-autoregressive text generation, yet existing samplers, which pick tokens to unmask based on model confidence, ignore interactions when unmasking multiple positions in parallel and effectively reduce to slow, autoregressive behavior. We propose the Dilated Unmasking Scheduler (DUS), an inference-only, planner-model-free method that partitions sequence positions into non-adjacent dilated groups and unmasks them in parallel so as to minimize an upper bound on joint entropy gain at each denoising step. By explicitly trading off the number of network calls against generation quality, DUS recovers most of the performance lost under traditional parallel unmasking strategies. Across math (GSM8K, MATH500), code (HumanEval, MBPP), general-knowledge (BBH, MMLU-Pro), and instruction following (IFEval) benchmarks, DUS outperforms confidence-based planners and turns the diffusion-specific quality-speed trade-off into a deterministic, predictable speedup set by the block size $B$, yielding up to $5.8\times$ wall-clock speedup over token-by-token MDLM decoding without modifying the underlying denoiser. Applied as a drop-in post-filter, dilated spacing also improves adaptive samplers. Code is available at https://github.com/omerlux/DUS.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **问题**：掩码扩散语言模型（Masked Diffusion Language Models, MDLMs）虽然承诺快速的非自回归文本生成，但现有并行解掩策略（如基于模型置信度选择 token）会忽略多个位置同时解掩时的位置交互，导致有效退化为缓慢的自回归行为，严重制约了速度提升。
- **背景**：扩散模型在连续域生成表现优异，但在离散文本生成中，MDLM 需要通过逐步解掩噪声序列来生成文本。如何在不牺牲生成质量的前提下大幅减少网络调用次数（即并行解掩），是核心挑战。
- **研究意义**：本文提出一种全新的、无需额外规划模型的推理时调度方法，可显著缓解并行解掩带来的性能下降，为扩散语言模型的实际部署提供了理论支撑和实用工具。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：将序列位置划分为非相邻的空洞组（dilated groups），在每一步解掩时只并行地处理同一组内的位置，而不同步解掩相邻位置，从而避免因忽略位置间依赖而导致的联合熵损失。通过最小化每步联合熵增益的上界，在进行较少的网络调用时尽可能保留生成质量。
- **关键技术细节**：
  - **空洞调度（Dilated Unmasking Scheduler, DUS）**：给定一个块大小参数 $B$，将序列按间隔模式划分成 $B$ 个不相邻的子组。例如，对于序列 $[x_1, x_2, \dots, x_L]$，组 $k$ 包含索引 $k, k+B, k+2B, \dots$。每次只对其中一个组内的所有位置同时解掩，其他组保持掩码状态。经过若干轮（每轮 $B$ 次网络调用）即可完成整个序列的解掩。
  - **理论依据**：证明该调度方案最小化了每一步的联合熵增益上界，即在信息论意义上最大化每一步的“信息增量”，从而在相同总网络调用次数下获得更好的生成质量。
  - **实现方式**：该方法是一个纯推理时的调度器，无需额外训练或规划模型，可直接替换现有基于置信度的并行调度器。也可作为后处理滤波器（post-filter）应用于自适应采样器以进一步提升性能。
- **算法流程（文字说明）**：
  1. 给定序列长度 $L$ 和块大小 $B$，计算空洞组数量 $B$（或根据 $B$ 生成组）。
  2. 初始化：所有位置为掩码状态。
  3. 对于 $t=1$ 到 $T$ 步（$T$ 可等于 $B$）：
     - 选择当前要解掩的空洞组（按固定顺序或自适应）。
     - 在该组内的所有位置，基于当前模型（MDLM denoiser）进行并行解掩（预测并填充 token）。
     - 其他位置保持不变。
  4. 重复直到所有掩码位置被填充，或达到指定调用次数。
  5. 块大小 $B$ 控制效率与质量的折中：$B$ 越小，调用次数越多，质量越接近自回归；$B$ 越大，调用次数越少，速度越快，但可能损失一定质量。

## 3. 实验设计：使用了哪些数据集 / 场景，它的 benchmark 是什么，对比了哪些方法

- **数据集与场景**：
  - **数学**：GSM8K、MATH500
  - **代码**：HumanEval、MBPP
  - **通用知识**：BBH、MMLU-Pro
  - **指令跟随**：IFEval
- **Benchmark**：以 token 级逐 token 解掩（即完全自回归顺序）的 MDLM 解码作为无损质量基线，同时与基于置信度的并行解掩调度器（confidence-based planners）对比。
- **对比方法**：
  - 原始 MDLM 逐 token 解码（baseline）
  - 现有并行调度器（如基于置信度选择 mask 位置的方法）
  - 可能还包括自适应采样器（文中提到 DUS 作为 post-filter 也可以改善自适应采样器）
- **主要指标**：生成质量（如准确率、pass@k等）和墙钟加速比（wall-clock speedup）。

## 4. 资源与算力

- **未明确说明**：论文摘要与元数据中未提及所使用的 GPU 型号、数量、训练时长等计算资源信息。仅提及代码开源，但无具体算力细节。可以推断实验在标准学术 GPU 集群上完成，但无法给出确切数字。

## 5. 实验数量与充分性

- **实验数量**：覆盖 7 个不同领域的数据集（数学×2、代码×2、推理×2、指令×1），每个数据集应有多组结果（不同块大小 $B$ 下的质量与速度比较）。此外可能包含消融实验，如 DUS 作为后处理滤波器对不同自适应采样器的影响。
- **充分性**：数据集多样性高，涵盖数学推理、代码生成、常识推理和指令跟随，基本能代表生成任务的主流类型。实验设计客观，与强基线（逐 token 解码）和并行调度器对比。但缺少对更大规模模型（如数十亿参数）或更长序列生成的实验，也未讨论在非英语语言上的表现。
- **公平性**：由于方法不修改模型，与对比方法在同一预训练 denoiser 上比较，保证了实验公平性。

## 6. 论文的主要结论与发现

- DUS 在相同网络调用次数下，显著优于基于置信度的并行解掩策略，恢复大部分因并行化而损失的性能。
- 在 GSM8K 等任务上，DUS 能以更少的网络调用达到接近自回归顺序解码的质量水平。
- 通过调节块大小 $B$，可将扩散特定的质量-速度trade-off转化为确定性、可预测的加速比：加速比高达 $5.8\times$（墙钟速度），且无需修改底层 denoiser。
- 空洞分组策略作为后处理滤波器也能提升自适应采样器的性能。

## 7. 优点：方法或实验设计上的亮点

- **创新性**：首次从信息论角度（最小化联合熵增益上界）设计并行解掩调度，理论根基扎实。
- **实用性**：推理时直接应用，无需额外训练或规划模型，即插即用；可替代现有调度器，也可作为后处理增强。
- **效率**：加速比高达 $5.8\times$，大幅提升实际部署中的生成速度。
- **实验全面**：覆盖多个高难度基准，验证了方法的泛化能力。
- **可解释性**：块大小 $B$ 提供了清晰的加速-质量调节旋钮，便于实际应用选择。

## 8. 不足与局限：包括实验覆盖、偏差风险、应用限制等

- **实验覆盖局限**：
  - 未在超大规模模型（如 70B 参数）或超长文本生成（如小说段落）上进行验证。
  - 仅涉及文本生成，未探索跨模态或条件生成场景（如文本到图像）。
  - 缺乏对非英语语种的评估，可能存在语言依赖性。
- **偏差风险**：空洞分组固定顺序可能不是最优的，论文未讨论如何自适应决定每组顺序以最大化效率。
- **应用限制**：依赖于离散扩散框架，若底层生成模型不是 MDLM（如连续扩散或自回归模型）则无法直接适用。且需要一定推理预算来保持质量（极快速度下质量下降仍不可避免）。
- **理论局限**：最小化的是熵增益的上界而非真实值，实际效果可能依赖于模型特性。

（完）
