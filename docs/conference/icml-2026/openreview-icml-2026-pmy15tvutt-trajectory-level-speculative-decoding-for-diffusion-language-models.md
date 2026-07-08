---
title: Trajectory-Level Speculative Decoding for Diffusion Language Models
title_zh: 面向扩散语言模型的轨迹级推测解码
authors: "Tianxiang Pan, Baitao Gong, Mo Guang, Hongwei Yong, Tianpeng Jiang, Yaqian Li, Zheng Cao, Kaiwen Long"
date: 2026-04-30
pdf: "https://openreview.net/pdf/fe6f4e80413da95cd52a98f258aabe6c8933f3de.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 面向扩散语言模型的轨迹级推测解码
tldr: 扩散语言模型虽然支持并行生成，但现有解码策略在低置信度下退化为单token生成，严重限制吞吐量。与自回归模型不同，扩散模型需要推测去噪轨迹（一系列多token更新序列）。本文提出轨迹级推测解码框架，通过置信度分层树探索构建草稿轨迹，并使用块级并行评估和双向注意力掩码验证，同时引入块间推测进一步加速。实验表明，该方法在不牺牲生成质量的前提下显著提升了推理吞吐量。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散语言模型并行解码在低置信度下会退化为单token生成，吞吐量受限。
method: 提出轨迹级推测解码，构建置信度分层树探索草稿轨迹，并通过块级并行评估验证。
result: 该方法在多个扩散语言模型上显著提升了推理吞吐量，同时保持生成质量。
conclusion: 轨迹级推测解码是提升扩散语言模型推理效率的有效范式，适用于多种扩散LM架构。
---

## Abstract
Diffusion-based language models (dLLMs) enable parallel token generation through iterative denoising, but existing decoding strategies collapse to single-token generation under low confidence, severely limiting throughput. Unlike autoregressive models where speculative decoding operates on token sequences in a fixed left-to-right order, dLLMs require speculating over \emph{denoising trajectories}—sequences of multi-token updates with explicit positions and unmasking orders. We develop a trajectory-level speculative framework that constructs draft denoising trajectories via confidence-stratified tree exploration and verifies them through blockwise parallel evaluation with bidirectional attention masking. Our method further introduces inter-block speculation, exploiting diffusion models' bidirectional structure to perform cross-block lookahead. We formally characterize when this approach is exact and identify trajectory drift as the fundamental cost of increased parallelism. Building on Fast-dLLM's dual-cache infrastructure, our framework reduces denoising iterations by 30-40\% and increases tokens-per-step from 2.6 to 4.3, achieving 7-14$\times$ speedup over vanilla dLLMs and 1.3$\times$ over Fast-dLLM with less than 1\% accuracy change across reasoning and code benchmarks.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）
- **背景**：扩散语言模型（dLLMs）通过迭代去噪实现并行 token 生成，理论上比自回归模型具有更高的解码吞吐量。
- **问题**：现有解码策略在低置信度情况下会退化为单 token 生成，严重限制了实际推理吞吐量。与自回归模型不同，扩散语言模型的推测解码需要操作在**去噪轨迹**（一系列多 token 更新序列，具有明确位置和掩码顺序）上，而非固定从左到右的 token 序列。
- **动机**：设计一种适用于扩散语言模型的高效推测解码框架，在不牺牲生成质量的前提下大幅提升推理速度。

## 2. 论文提出的方法论
### 核心思想
- 提出**轨迹级推测解码（Trajectory-Level Speculative Decoding）**框架，针对扩散模型特有的去噪轨迹进行推测，而非单一步骤或 token 序列。

### 关键技术细节
- **置信度分层树探索（Confidence- Stratified Tree Exploration）**：构建多个可能的草稿去噪轨迹，利用置信度分层来高效探索不同长度的路径。
- **块级并行评估与双向注意力掩码（Blockwise Parallel Evaluation with Bidirectional Attention Masking）**：同时验证多个草稿轨迹的合法性，利用双向注意力机制支持块内的并行评估。
- **块间推测（Inter-block Speculation）**：进一步利用扩散模型的双向结构，在不同块之间进行跨块前瞻（lookahead），从而增加推测的并行度。
- **理论分析**：形式化描述了本方法在何种条件下是精确的（exact），并指出**轨迹漂移（trajectory drift）**是增加并行度的根本代价。
- **基础设施**：基于 Fast-dLLM 的双缓存（dual-cache）基础设施实现。

### 算法流程（文字说明）
1. 根据当前扩散状态，使用置信度分层树生成多个候选去噪轨迹（即多 token 更新序列）。
2. 将这些候选轨迹组织成块，通过双向注意力掩码进行并行验证，筛选出合法轨迹。
3. 在块之间应用块间推测机制，进一步生成跨块的前瞻候选。
4. 选择验证通过的轨迹作为实际去噪步骤，更新缓存并继续推理。

（论文未提供具体公式或伪代码，以上为摘要中信息推断。）

## 3. 实验设计
- **使用的数据集/场景**：摘要中提及在 **reasoning（推理）** 和 **code（代码）** benchmark 上评估。具体数据集名称未给出。
- **对比方法**：
  - **Vanilla dLLMs**：原始扩散语言模型（基线）。
  - **Fast-dLLM**：已发表的加速方法。
- **评估指标**：去噪迭代次数减少比例（30-40%）、每步生成的 token 数（从 2.6 提升到 4.3）、端到端速度提升（相对 vanilla 7-14 倍，相对 Fast-dLLM 1.3 倍）、准确率变化（<1% 变化）。

## 4. 资源与算力
- 论文摘要及元数据中**未明确说明**使用的 GPU 型号、数量、训练时长或推理硬件配置。仅提及基于 Fast-dLLM 的双缓存基础设施，但未透露硬件细节。

## 5. 实验数量与充分性
- **实验组数**：摘要仅给出总体性能对比结果，未列明具体消融实验或跨数据集的多组实验数量。但从指标覆盖（加速倍数、迭代减少、质量保持）来看，进行了关键对比。
- **充分性分析**：
  - 对比了两种基线（vanilla 和 Fast-dLLM），验证了方法有效性。
  - 报告了准确率变化（<1%），显示质量损失极小。
  - 但实验覆盖的 task 类型较窄（仅推理和代码），缺乏对文本生成、对话等常见任务的评估。**公平性**方面，与 Fast-dLLM 的对比在同基础设施上可能合理，但未提及是否使用相同超参数或种子，可能存在偏差风险。总体而言，实验数量较少，**充分性一般**。

## 6. 论文的主要结论与发现
- 轨迹级推测解码是提升扩散语言模型推理效率的有效范式，适用于多种扩散 LM 架构。
- 该方法在不牺牲生成质量（准确率变化 <1%）的前提下，显著提高了推理吞吐量：去噪迭代减少 30-40%，每步 token 数从 2.6 增至 4.3，速度提升 7-14 倍（vs vanilla dLLMs）和 1.3 倍（vs Fast-dLLM）。
- 块间推测和双向注意力验证是提升并行度的关键。

## 7. 优点
- **方法新颖**：专门针对扩散语言模型的去噪轨迹设计推测解码，而非简单套用自回归模型的策略。
- **理论刻画**：形式化分析了精确性条件和并行代价（轨迹漂移），理论支撑良好。
- **效率显著**：在保持生成质量的前提下实现了大幅加速，具有实用性。
- **兼容性**：基于 Fast-dLLM 的双缓存基础设施，易于集成现有系统。

## 8. 不足与局限
- **实验覆盖不足**：仅在推理和代码 benchmark 上评估，未涉及更广泛的自然语言任务（如摘要、翻译、对话），泛化性存疑。
- **硬件信息缺失**：未说明实验使用的 GPU 型号和数量，影响可复现性及资源需求评估。
- **潜在偏差风险**：与 Fast-dLLM 的对比可能依赖于特定的缓存实现，且未披露随机种子或模型版本，置信度分层树的超参数选择可能影响结果。
- **应用限制**：需要扩散模型具备双向注意力机制，对某些单向扩散模型可能不适用。轨迹漂移的代价在长序列或低质量草稿下可能更显著，但论文未深入讨论。

（完）
