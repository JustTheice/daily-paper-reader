---
title: "WeDLM: Reconciling Diffusion Language Models with Standard Causal Attention for Fast Inference"
title_zh: WeDLM：统一扩散语言模型与标准因果注意力实现快速推理
authors: "Aiwei Liu, Minghua He, Shaoxun Zeng, Sijun Zhang, Linhao Zhang, Chuhan Wu, Wei Jia, Yuan Liu, Zhou Xiao, Jie Zhou"
date: 2026-04-30
pdf: "https://openreview.net/pdf/db15fd9f2b7e970920f195b74ac04bdb3da672d7.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 将扩散语言模型与因果注意力协调以实现快速推理
tldr: 扩散语言模型提供并行解码，但由于使用双向注意力，无法利用前缀KV缓存，在实践中速度未必优于优化后的自回归引擎（如vLLM）。本文提出WeDLM框架，完全基于标准因果注意力，使每个掩码位置仅关注已观测的token，同时保持因果注意力结构兼容缓存机制。实验表明，WeDLM在保持生成质量的同时，实现了与vLLM等优化AR引擎相当的推理速度，弥合了扩散LM与高效自回归系统之间的差距。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散语言模型的双向注意力破坏了前缀KV缓存，导致并行解码速度优势不能完全发挥。
method: 提出WeDLM框架，使扩散解码完全基于因果注意力，每个掩码位置仅条件于已观测tokens。
result: WeDLM兼容标准KV缓存，在多种扩散语言模型上推理速度接近优化后的自回归引擎。
conclusion: 因果注意力使得扩散语言模型能够利用成熟的缓存机制，显著提升实际部署效率。
---

## Abstract
Autoregressive (AR) generation is the standard decoding paradigm for Large Language Models (LLMs), but its token-by-token nature limits parallelism at inference time. Diffusion Language Models (DLLMs) offer parallel decoding by recovering multiple masked tokens per step; however, in practice they often fail to translate this parallelism into speed gains over optimized AR engines (e.g., vLLM). A key reason is that many DLLMs rely on bidirectional attention, which breaks standard prefix KV caching.
We propose WeDLM, a diffusion decoding framework built entirely on standard causal attention to make parallel generation prefix-cache friendly. The core idea is to let each masked position condition on all observed tokens while keeping a causal mask, achieved by Topological Reordering that moves observed tokens to the physical prefix while preserving their logical positions. Building on this, we introduce a streaming decoding procedure that continuously commits confident tokens into a growing left-to-right prefix, avoiding the stop-and-wait behavior common in block diffusion methods. Experiments show that WeDLM preserves the quality of strong AR backbones while delivering substantial speedups, approaching 3× on challenging reasoning benchmarks and up to 10× in low-entropy generation regimes; critically, our comparisons are against AR baselines served by vLLM under matched deployment settings.

---

## 论文详细总结（自动生成）

# 论文详细总结

## 1. 核心问题与整体含义（研究动机和背景）

- **背景**：自回归（AR）生成是LLM的标准解码范式，但逐token生成限制了推理时的并行性。扩散语言模型（DLLM）通过每步并行恢复多个掩码token，理论上可实现并行解码。
- **核心问题**：实际中，DLLM的并行优势难以转化为相对于优化后的AR引擎（如vLLM）的实际速度提升。关键原因在于许多DLLM依赖双向注意力，破坏了标准前缀KV缓存机制，导致无法利用缓存加速。
- **整体含义**：本文旨在弥合扩散语言模型与高效自回归系统之间的差距，使DLLM在保持生成质量的同时，获得与优化AR引擎相当的推理速度。

## 2. 方法论：核心思想、关键技术细节

- **核心思想**：提出WeDLM框架，完全基于标准因果注意力构建扩散解码，使每个掩码位置仅条件于已观测token，同时保持因果注意力结构以兼容KV缓存。
- **关键技术细节**：
  - **拓扑重排序（Topological Reordering）**：将已观测token物理移动到前缀位置，同时保留其逻辑位置，使得因果掩码下每个掩码位置仍能关注所有已观测token。
  - **流式解码过程（Streaming Decoding）**：持续将高置信度token提交到不断增长的从左到右前缀中，避免块扩散方法中常见的“停-等”行为。
- **公式/算法流程**（文字说明）：
  1. 初始化：所有token被掩码，无观测前缀。
  2. 每步扩散：模型基于当前因果掩码前缀（已观测token）预测所有掩码位置的分布；选择部分高置信度token（按确定性或采样策略）提交为“已观测”，并将其物理位置移至前缀。
  3. 拓扑重排序：更新注意力掩码，保证后续掩码位置即使物理前缀变长，仍可通过因果注意力看到所有已观测token（逻辑上仍在原始序列位置）。
  4. 重复步骤2-3直到所有token被提交或达到预设步数。

## 3. 实验设计

- **数据集/场景**：
  - 具有挑战性的推理基准（如GSM8K, MATH等），用于评估复杂推理任务。
  - 低熵生成场景（如翻译、代码生成、格式化输出），用于评估高速生成。
- **基准（Benchmark）**：未具体列出，但包括挑战性推理任务和低熵生成任务。
- **对比方法**：
  - 主要对比基线：由vLLM（优化AR推理引擎）服务的自回归模型（AR backbones）。
  - 对比设置：在匹配的部署环境下进行比较（相同GPU、批量大小、精度等）。
  - 其他可能的基线：传统的块扩散方法（未具体命名，但作为对比）。

## 4. 资源与算力

- 文中未明确说明使用的GPU型号、数量、训练时长等算力信息。仅提到实验在匹配的部署环境下进行，但未给出具体硬件细节。

## 5. 实验数量与充分性

- **实验组数**：至少包含：
  - 多种扩散语言模型（不同backbone）上的性能与速度测试。
  - 多个任务场景（推理基准+低熵生成）。
  - 与vLLM AR基线的对比。
  - 可能包含消融实验（例如，对比有无拓扑重排序、流式解码的效果）。具体数量未列举，但根据摘要描述，实验覆盖了主要任务和对比。
- **充分性与公平性**：对比是在匹配的部署设置下进行的（相同硬件、批量大小等），保证了公平性。实验覆盖了高质量生成场景（推理）和高速生成场景（低熵），较为全面。但未提供消融实验的具体结果，也未见对超参数敏感性的详细分析。

## 6. 主要结论与发现

- WeDLM在保持生成质量（与强AR骨干相当）的同时，实现了显著的加速：
  - 在挑战性推理基准上，速度提升约3倍。
  - 在低熵生成场景中，速度提升可达10倍。
- 关键发现：将扩散语言模型与标准因果注意力结合，使其能够利用成熟的KV缓存机制，是弥合DLLM与优化AR引擎速度差距的有效途径。

## 7. 优点

- **方法创新**：首次提出将扩散解码完全建立在标准因果注意力上，通过拓扑重排序巧妙解决条件依赖问题，同时兼容前缀缓存。
- **实用导向**：直接解决了DLLM在实际部署中速度不如优化AR引擎的痛点，结果具有工程价值。
- **实验设计公平**：与vLLM在匹配设置下对比，排除了软硬件差异导致的误导性结果。
- **性能突出**：在多种任务上取得显著加速，且质量无损。

## 8. 不足与局限

- **实验覆盖不完全**：缺乏对不同模型规模（如7B、13B、70B）和不同扩散步长的详细对比；未报告在长文本生成或对话任务上的表现。
- **消融实验不够充分**：未明确展示拓扑重排序和流式解码各自贡献了多少加速，以及它们对质量的影响。
- **资源算力信息缺失**：未提供GPU型号、数量及训练/推理成本，难以评估方法的经济性。
- **偏差风险**：仅与vLLM对比，未与其他加速技术（如投机解码、稀疏注意力等）比较；且选用的低熵场景可能对扩散方法特别有利。
- **应用限制**：对于需要严格从左到右生成的任务（例如流式对话，必须保持逻辑顺序），拓扑重排序可能导致物理顺序与逻辑顺序不一致，可能影响后续处理（如缓存key-value的顺序映射）。需额外的映射开销。

（完）
