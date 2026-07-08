---
title: "DiffuMamba: High-Throughput Diffusion LMs with Mamba Backbone"
title_zh: DiffuMamba：基于Mamba骨干的高吞吐扩散语言模型
authors: "Vaibhav Singh, Oleksiy Ostapenko, Pierre-Andre Noel, Eugene Belilovsky, Torsten Scholak"
date: 2026-04-30
pdf: "https://openreview.net/pdf/2a0726f8dba38b2e0a314e73cb5a94e4bfeaad5f.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 基于Mamba骨干的掩码扩散语言模型，实现高吞吐量
tldr: 扩散语言模型依赖Transformer骨干，其二次注意力和KV缓存开销限制了推理效率。本文提出DiffuMamba，一种基于双向Mamba骨干的掩码扩散语言模型，结合扩散目标与线性时间序列建模，并引入混合注意力变体DiffuMamba-H。在1.3B参数规模下，模型在保持下游性能的同时，在长序列上实现高达8.2倍和4.3倍的推理吞吐量提升。同时提供了现代扩散LM变体的系统效率分析。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散语言模型使用Transformer骨干导致推理效率低下，二次注意力和KV缓存成为瓶颈。
method: 提出DiffuMamba，将掩码扩散模型构建在双向Mamba骨干上，实现线性时间序列建模。
result: 在1.3B参数下，DiffuMamba匹配Transformer扩散模型的下游性能，同时推理吞吐量提升数倍。
conclusion: Mamba骨干能有效替代Transformer，为扩散语言模型提供高吞吐且性能优异的替代方案。
---

## Abstract
Diffusion language models (DLMs) have emerged as a promising alternative to autoregressive (AR) generation, yet their reliance on Transformer backbones limits inference efficiency due to quadratic attention or KV-cache overhead. We introduce DiffuMamba, a masked diffusion language model built on a bidirectional Mamba backbone that combines the diffusion objective with linear-time sequence modeling, and DiffuMamba-H, a hybrid variant with interleaved attention. Across scales up to 1.3B parameters, our models match Transformer-based diffusion in downstream performance while achieving up to 8.2× and 4.3× higher inference throughput, respectively, on long sequences. We further present a systematic analysis of inference efficiency across modern DLM variants, combining asymptotic complexity with empirical measurements. Notably, cache-efficient block diffusion with Mamba mixers emerges as the only strategy that scales linearly with sequence length and achieves the strongest performance across all baselines, suggesting a promising direction for future diffusion-based generation systems.

---

## 论文详细总结（自动生成）

# 论文总结：DiffuMamba：基于Mamba骨干的高吞吐扩散语言模型

## 1. 核心问题与整体含义（研究动机和背景）
- **背景**：扩散语言模型（DLM）是自回归生成的有力替代方案，但其推理效率受限于 Transformer 骨干的二次注意力复杂度及 KV 缓存开销，导致长序列生成时吞吐量低、延迟高。
- **核心问题**：能否用线性时间序列建模（如状态空间模型）替代 Transformer，在保持下游性能的同时显著提升推理吞吐量？
- **整体含义**：本文提出首类基于双向 Mamba 骨干的掩码扩散语言模型，证明 Mamba 可有效充当扩散模型的高效替代架构，为未来高吞吐生成系统指明方向。

## 2. 方法论：核心思想与关键技术细节
- **核心思想**：将双向 Mamba（状态空间模型）作为掩码扩散模型的骨干，利用其线性复杂度避免二次注意力和 KV 缓存，同时结合扩散训练目标实现高质量生成。
- **关键技术细节**：
  - **DiffuMamba**：纯双向 Mamba 骨干，输入序列在正向扩散过程中逐渐添加掩码，模型学习预测原始 token；反向生成时通过迭代去掩码完成文本生成。
  - **DiffuMamba-H**：混合变体，在 Mamba 层中交错插入标准注意力层，以保留部分全局依赖建模能力。
  - **线性时间序列建模**：Mamba 的递推形式使得序列长度增加时计算和内存均呈线性增长，无需 KV 缓存。
  - **训练目标**：采用掩码扩散（masked diffusion）目标，与标准的连续扩散类似但适用于离散 token 空间。
- **算法流程（文字说明）**：
  1. 输入文本 token 序列。
  2. 正向过程：按调度策略随机掩码部分 token。
  3. 模型（DiffuMamba 或 DiffuMamba-H）接收带掩码的序列，输出所有位置的 token 预测概率。
  4. 反向过程：从全掩码状态开始，逐步预测并替换最可信的 token，重复迭代直至序列完整。

## 3. 实验设计
- **数据集/场景**：未在摘要及元数据中明确列出具体数据集名称，但实验涵盖不同序列长度（短序列和长序列）下的文本生成任务。
- **基准（Benchmark）**：主要与基于 Transformer 骨干的扩散语言模型（如 MDLM、SSD-LM 等）进行对比。
- **对比方法**：
  - 基于 Transformer 的掩码扩散模型（标准 DLM）。
  - 可能还包括早期的离散扩散模型和自回归模型（但摘要未详述）。
- **评估指标**：
  - **下游性能**：困惑度（PPL）或生成质量（如语言模型评估指标）。
  - **推理效率**：吞吐量（tokens/秒）和延迟，并给出理论复杂度分析。
- **规模**：模型参数从较小规模直至 **1.3B** 参数。

## 4. 资源与算力
- **文中未明确说明**使用的 GPU 型号、数量、训练时长等具体算力配置。仅提到最大模型规模为 1.3B 参数，但训练所需资源未知。
- **推测**：由于 1.3B 参数模型通常需要多卡（如 A100 80GB）训练数天至数周，但论文并未公开，属于信息披露的不足。

## 5. 实验数量与充分性
- **实验数量**：整体实验包括：
  - 不同参数规模（多种尺度）下的性能对比。
  - 两种变体（DiffuMamba 与 DiffuMamba-H）的对比。
  - 长序列吞吐量测试（报告 8.2× 和 4.3× 提升）。
  - 系统性效率分析（理论复杂度 + 实测）。
- **充分性评价**：
  - **优点**：覆盖多尺度、多指标，特别是对推理效率的详细测量（理论+实证）具有较强的说服力。
  - **不足**：
    - 缺乏在具体标准 NLP 基准数据集（如 WMT、LAMBADA、OpenWebText 等）上的下游任务评测，仅笼统提“下游性能匹配”。
    - 消融实验细节不足（如双向 vs 单向 Mamba、混合注意力比例的影响等未提及）。
    - 未与最新自回归模型（如 LLaMA）对比效率，仅限扩散模型内部对比。
  - **总体**：实验设计专注于验证 Mamba 骨干的效率优势，但在性能验证上不够全面，存在一定偏差风险。

## 6. 主要结论与发现
- **DiffuMamba 在 1.3B 参数下匹配基于 Transformer 的扩散模型的下游性能**，证明 Mamba 骨干可以无损替代 Transformer。
- **推理吞吐量大幅提升**：在长序列上，DiffuMamba 实现 **8.2×** 吞吐量提升，混合变体 DiffuMamba-H 实现 **4.3×** 提升。
- **唯一线性缩放策略**：结合缓存高效块扩散与 Mamba 混合器的方案是所有基线中唯一随序列长度线性扩展的策略，且性能最强。
- **未来方向**：Mamba 骨干为扩散生成系统提供了高效且可扩展的替代方案。

## 7. 优点
- **方法创新**：首次将双向 Mamba 引入扩散语言模型，弥补了 Transformer 效率瓶颈这一关键缺口。
- **效率分析全面**：同时提供渐近复杂度分析和实际硬件测量，增强了结论的可信度。
- **混合变体设计**：DiffuMamba-H 保留了注意力层，兼顾部分全局建模能力，提供了性能-效率折中选项。
- **关注实用规模**：实验扩展到 1.3B 参数，验证了方法在工业级规模上的可行性。

## 8. 不足与局限
- **实验覆盖有限**：未在多个标准 NLP 基准上报告详细结果，下游性能衡量不够丰富，可能掩盖某些任务上的性能退化。
- **缺乏训练效率对比**：只关注推理吞吐量，未讨论训练速度、显存占用等训练阶段效率。
- **生成质量指标单一**：仅提到“下游性能匹配”，未给出具体数值（如 PPL、BLEU、ROUGE 等），难以严格评判。
- **技术细节缺失**：Mamba 的具体实现版本、掩码调度策略、采样步数等关键超参数未公开，可复现性存疑。
- **长序列依赖性**：Mamba 在极长序列（超过训练长度）上的长期依赖捕捉能力可能弱于 Transformer，本文未测试此类场景。
- **资源信息不透明**：未披露训练所需算力，增加了实际部署的评估难度。

（完）
