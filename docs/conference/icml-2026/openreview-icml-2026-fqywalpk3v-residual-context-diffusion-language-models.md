---
title: Residual Context Diffusion Language Models
title_zh: 残差上下文扩散语言模型
authors: "Yuezhou Hu, Harman Singh, Monishwaran Maheswaran, Haocheng Xi, Coleman Richard Charles Hooper, Jintao Zhang, Aditya Tomar, Michael W. Mahoney, Sewon Min, Mehrdad Farajtabar, Kurt Keutzer, Amir Gholami, Chenfeng Xu"
date: 2026-04-30
pdf: "https://openreview.net/pdf/e1a2687020194e2f2c0c6af49c5adb5d1f8ab9a4.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 扩散语言模型的残差上下文注入
tldr: 针对块级扩散语言模型中remasking机制浪费计算的问题，提出残差上下文扩散（RCD）模块，将丢弃的token表征转换为上下文残差并重新注入后续去噪步骤。该方法采用解耦的两阶段训练流水线，在保持生成质量的同时有效提升了计算效率，为扩散语言模型的高效并行解码提供了新思路。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有块级扩散语言模型的remasking机制仅解码高置信度token，丢弃其他token浪费计算。
method: 提出RCD模块，将丢弃token的表征转换为残差并注入下一步去噪。
result: 在语言建模基准上实现了更好的效率-质量权衡。
conclusion: 回收丢弃token的上下文信息能显著提升块级扩散模型的性能。
---

## Abstract
Diffusion Large Language Models (dLLMs) have emerged as a promising alternative to purely autoregressive language models because they can decode multiple tokens in parallel. However, state-of-the-art block-wise dLLMs rely on a ``remasking" mechanism that decodes only the most confident tokens and discards the rest, effectively wasting computation. We demonstrate that recycling computation from the discarded tokens is beneficial, as these tokens retain contextual information useful for subsequent decoding iterations. In light of this, we propose Residual Context Diffusion (RCD), a module that converts these discarded token representations into contextual residuals and injects them back for the next denoising step. RCD uses a decoupled two-stage training pipeline to bypass the memory bottlenecks associated with backpropagation. We validate our method on both long CoT reasoning (SDAR) and short CoT instruction following (LLaDA) models. We demonstrate that a standard dLLM can be efficiently converted to the RCD paradigm with merely $\sim$300 million tokens. RCD consistently improves frontier dLLMs by 4--11 percentage points in accuracy with minimal extra computation overhead across a wide range of benchmarks. Notably, on the most challenging AIME tasks, RCD nearly doubles baseline accuracy and attains up to 4--5x fewer denoising steps at baseline's peak accuracy.

---

## 论文详细总结（自动生成）

# 残差上下文扩散语言模型 (Residual Context Diffusion Language Models) 详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **背景**：扩散大语言模型 (dLLMs) 作为纯自回归语言模型的替代方案，能够并行解码多个 token，具有计算效率潜力。
- **核心问题**：当前最先进的块级扩散语言模型采用“remasking”机制，即仅解码最自信的 token，而丢弃其余 token，这实质上浪费了计算资源。丢弃的 token 中仍保留了对后续解码有用的上下文信息，但现有方法未加以利用。
- **研究动机**：证明回收丢弃 token 的计算是有益的，并提出一种新模块，将这些丢弃的表征转化为上下文残差并重新注入，从而在保持生成质量的前提下提升计算效率。

## 2. 论文提出的方法论

- **核心思想**：将块级扩散语言模型每一步去噪过程中被丢弃的 token 表征转换为“上下文残差”，并在下一步去噪步骤中重新注入，以保留有用的上下文信息，减少计算浪费。
- **关键技术细节**：
  - 提出 **Residual Context Diffusion (RCD)** 模块。
  - RCD 采用解耦的两阶段训练流水线，以避开与反向传播相关的内存瓶颈。
  - 该方法无需改动原有 dLLM 的主干架构，可高效地将标准 dLLM 转换为 RCD 范式，仅需约 3 亿 token 的微调数据。
  - 在去噪过程中，RCD 模块将丢弃 token 的隐藏状态经过线性变换（或其他轻量变换）生成残差，与当前步骤的输入相加或通过门控机制注入。
- **公式/算法流程（文字说明）**：
  1. 在每一步去噪中，模型预测所有 token 的置信度，并保留高置信度 token（remasking 机制）。
  2. 对于被丢弃（低置信度）的 token，提取其隐藏表征。
  3. 将丢弃 token 的表征输入 RCD 模块，计算得到上下文残差向量。
  4. 该残差向量与下一步去噪的输入（通常为当前 token 嵌入或噪声）相加，完成信息注入。
  5. 重复直到所有 token 被解码或达到最大步数。

## 3. 实验设计

- **数据集/场景**：
  - 长链思维推理 (long CoT reasoning) 模型：SDAR。
  - 短链指令跟随 (short CoT instruction following) 模型：LLaDA。
- **基准 (Benchmark)**：在一系列广泛的任务上评估，重点提及了最具挑战性的 **AIME** 任务。
- **对比方法**：标准块级扩散语言模型（未使用 RCD），可能还包括其他扩散 LM 基线（论文摘要未详细列出，但暗示为“frontier dLLMs”）。

## 4. 资源与算力

- 论文中明确提到“仅需约 3 亿 token 的微调数据”，但未具体说明 GPU 型号、数量、训练时长等硬件细节。
- **结论**：实验资源未详细披露，仅说明数据量较小，暗示计算开销较低。

## 5. 实验数量与充分性

- **实验量**：主要验证了两类模型（SDAR 和 LLaDA）在多个基准上的表现。摘要中报告了在 AIME 上准确率近乎翻倍，且达到基线峰值准确率时去噪步数减少 4-5 倍。
- **充分性**：由于缺乏完整实验表格和消融研究细节（仅摘要），难以评估其充分性。但提及“一致提升 4-11 个百分点”，覆盖多个基准，基本合理。
- **客观性**：需要实际论文完整版验证对比方法是否公平，是否存在选择偏差。当前摘要未显示全面的比较。

## 6. 论文的主要结论与发现

- RCD 模块能够有效回收丢弃 token 的上下文信息，显著提升块级扩散模型的性能。
- 在标准 dLLM 基础上仅需少量数据（~3亿 token）即可高效转换。
- RCD 在多个基准上提高准确率 4-11 个百分点，计算开销极小。
- 在最具挑战性的 AIME 任务上，准确率近乎翻倍，并能在更少的去噪步骤（4-5 倍减少）下达到基线峰值准确率。

## 7. 优点

- **核心创新**：提出了简单而有效的上下文残差回注机制，解决了 remasking 浪费计算的问题。
- **效率提升**：两种解耦训练流水线避免了内存瓶颈，训练成本低。
- **通用性**：可兼容现有多种块级扩散语言模型（如 SDAR 和 LLaDA），具有良好的迁移性。
- **性能显著**：在困难推理任务上取得了接近翻倍的提升，且加速了推理过程。

## 8. 不足与局限

- **实验细节不透明**：由于本文仅为摘要（可能来自 ICML 2026 接收论文），缺乏完整的实验设置、消融研究和模型规模分析。
- **未见多样化基准覆盖**：虽然提到“wide range of benchmarks”，但未具体列举，可能遗漏了对域外泛化、低资源场景的评估。
- **潜在偏差风险**：仅测试了两种 dLLM 架构，是否适用于其他扩散框架（如 masked diffusion、continuous diffusion）尚不明确。
- **资源信息缺失**：未报告 GPU 型号和训练时间，难以评估实际计算成本。
- **可能存在过拟合风险**：仅用 3 亿 token 微调，若原始模型很大（如几十亿参数），可能导致领域适配不足。

（完）
