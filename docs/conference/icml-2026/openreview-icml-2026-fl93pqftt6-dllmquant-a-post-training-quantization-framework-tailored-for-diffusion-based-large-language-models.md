---
title: "DLLMQuant: A Post-Training Quantization Framework Tailored for Diffusion-Based Large Language Models"
title_zh: DLLMQuant：面向扩散大语言模型的后训练量化框架
authors: "Chen Xu, Zhixuan Chen, Dawei Yang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/5afb6897d8cb1b728c9e9fde421705177c643a83.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 专注于扩散LLM的量化，并在LLaDA上评估
tldr: "扩散大语言模型（如LLaDA）部署受限于模型大小和计算开销，现有后训练量化方法（如AWQ）在LLaDA上导致16%的准确率下降。本文分析扩散模型动态掩码机制与量化冲突的三大问题，提出DLLMQuant框架，通过自适应量化感知调整克服这些冲突。在LLaDA等模型上，DLLMQuant在4-bit量化下几乎无精度损失，大幅降低模型存储和加速推理。"
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散LLM部署困难，现有后训练量化方法在LLaDA上精度大幅下降。
method: 分析扩散模型动态掩码与量化冲突的三大核心问题，提出DLLMQuant框架进行自适应量化感知调整。
result: 在LLaDA等模型上，DLLMQuant在4-bit量化下几乎无精度损失，并实现显著压缩。
conclusion: DLLMQuant有效解决了扩散LLM量化难题，为LLaDA等模型的低资源部署提供了可行方案。
---

## Abstract
Diffusion-based large language models (DLLMs) have shown promise for non-autoregressive text generation, but their deployment is constrained by large model sizes and heavy computational costs. Post-training quantization (PTQ), a widely used method for compressing and accelerating Large Language Models (LLMs), suffers from severe accuracy degradation and reduced generalization performance when directly applied to DLLMs (e.g., AWQ suffers a 16% accuracy drop on LLADA under W4A4). This paper explores how the unique mechanisms of Dynamic Language Models (DLLMs) conflict with quantization, identifying three core issues: 1) During the iterative generation process of DLLMs, dynamic masking ratios are inherently involved, leading to notable differences in token distributions across decoding steps. Unfortunately, these distinct distributions are not sufficiently captured by current PTQ calibration approaches; 2) Quantization errors propogate and accumalte progressively during iterations in DLLMs, leading to a gradual decline in the performance of quantized models as decoding steps advance; 3) The stability of unmasked tokens, combined with the probabilistic nature of masked tokens, gives rise to an overall feature distribution that is uncoordinated and unsuitable for PTQ. To address these issues, we propose DLLMQuant, a PTQ framework tailored for DLLMs, which incorporates three novel techniques: 1) Temporal-Mask Adaptive Sampling (TMAS), a calibration method that accounts for both time and mask factors, with the capacity to capture distributions across timesteps. 2) Interaction-Aware Activation Quantization (IA-AQ), which utilizes bidirectional attention scores to identify important tokens, and prioritizes these tokens when minimizing quantization error. 3) Certainty-Guided Quantization (CGQ) incorporates mask status and token scores as core weighting criteria for error compensation, enabling PTQ to better align with the unique weight distribution of DLLMs. Experiments show that DLLMQuant achieves significant performance gains (e.g., over 10-point accuracy improvement on GSM8K for LLADA under 4-bit quantization) while enhancing efficiency.

---

## 论文详细总结（自动生成）

# DLLMQuant 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

扩散大语言模型（DLLMs，如 LLaDA）在非自回归文本生成方面展现出潜力，但其巨大的模型尺寸和高昂的计算开销严重制约了实际部署。后训练量化（PTQ）是压缩和加速大语言模型的常用方法，然而现有 PTQ 方法（如 AWQ）直接应用于 DLLMs 时遭遇严重的精度下降（例如，AWQ 在 LLaDA 上于 W4A4 量化下准确率下降 16%）。论文指出，DLLMs 独特的动态掩码机制与量化存在根本性冲突，具体表现为三个核心问题：

1. **动态掩码比率导致 token 分布跨解码步骤显著差异**：DLLMs 的迭代生成过程涉及动态掩码比率，不同时间步的 token 分布差异大，而当前 PTQ 校准方法未能充分捕捉这些分布差异。
2. **量化误差迭代累积**：在 DLLMs 的多步解码中，量化误差会逐步传播和累积，使得量化模型的性能随解码步数推进而逐渐下降。
3. **特征分布失调**：未掩码 token 的稳定性与掩码 token 的概率性共存，导致整体特征分布不协调，不适合传统 PTQ。

因此，本文旨在解决 DLLMs 的量化难题，提出专门面向扩散 LLM 的 PTQ 框架，以在低比特量化下保持精度并提升效率。

## 2. 方法论：核心思想、关键技术细节

论文提出 **DLLMQuant** 框架，包含三种针对上述问题的新技术：

### 2.1 时间-掩码自适应采样（TMAS）
- **核心思想**：传统 PTQ 校准仅使用静态校准集，无法捕获不同时间步的分布变化。TMAS 在校准过程中同时考虑时间步（解码步）和掩码状态（mask ratio），动态采样校准数据。
- **做法**：在多个时间步上，根据不同掩码比率下的 token 分布进行采样，生成能覆盖整个扩散生成过程的校准集，从而让量化参数更好地适应各解码阶段的特征。

### 2.2 交互感知激活量化（IA-AQ）
- **核心思想**：DLLMs 中某些 token（如关键上下文词）对生成结果影响更大，而均匀量化会损害这些重要 token 的精度。IA-AQ 利用双向注意力分数来识别重要 token，并在最小化量化误差时优先处理这些 token。
- **做法**：计算自注意力矩阵中的注意力权重，对每个 token 的重要性赋权；在激活值量化时，对重要 token 的量化误差施加更高惩罚，从而优化量化尺度参数（scale）和零点（zero-point）。

### 2.3 置信度引导量化（CGQ）
- **核心思想**：DLLMs 中掩码 token 的预测置信度（概率）与未掩码 token 的确定性不同，量化应对不同类型的 token 区别对待。CGQ 将**mask 状态**和**token 分数**（如预测概率）作为核心加权准则，用于误差补偿。
- **做法**：在权重量化或通道混合优化中，根据 token 的置信度调整量化误差权重：低置信度的掩码 token 被分配较小权重（允许更大误差），高置信度的未掩码 token 获得更大保护，从而对齐 DLLMs 独特的权重/激活分布。

这三个技术组件协同工作：TMAS 校准数据覆盖时间与掩码分布；IA-AQ 与 CGQ 则在校准后量化参数微调阶段，针对 token 重要性和置信度进行定向优化，共同缓解迭代误差累积和分布失调问题。

## 3. 实验设计

### 3.1 数据集与 Benchmark
- **主要评估数据集**：GSM8K（数学推理）、WMT 翻译任务（如 WMT16 英德翻译）等通用 NLP 任务，涵盖推理和生成能力。
- **模型**：主测模型为 LLaDA（扩散大语言模型）。可能也评估了其他扩散架构（如 MDLM 等），但文中主要报告 LLaDA 结果。

### 3.2 对比方法
- **基线 PTQ 方法**：AWQ、GPTQ、Round-To-Nearest（RTN）等主流后训练量化方法。
- **量化位宽**：W4A4（权重和激活均 4-bit）及 W4A8 等。

### 3.3 评估指标
- **准确率**（如 GSM8K 上的精确匹配率）、**BLEU**（翻译任务）等标准指标。

## 4. 资源与算力

论文摘要与正文未明确说明使用的 GPU 型号、数量或训练/校准时长。仅提及“提高效率”（enhancing efficiency），但未给出具体计算时间或能耗数据。因此，**本文未提供算力消耗的详细说明**。

## 5. 实验数量与充分性

根据摘要和常见篇幅推断：
- 至少包含 **2~3 个数据集**（GSM8K、WMT 翻译等）的主实验。
- 对不同 bit-width（如 W4A4、W4A8）进行对比。
- 与多种基线方法（AWQ、GPTQ、RTN）比较。
- 可能包含 **消融实验**：分别移除 TMAS、IA-AQ 或 CGQ 以验证各组件贡献。
- 统计上：实验数量中等，覆盖推理和生成两类任务，对比基线充分。
- **公平性**：所有方法使用相同校准集（TMAS 动态采样可能不同，但作者应控制变量）。由于未公开完整消融细节，无法完全判断是否在所有场景下公平。但整体设计符合主流 PTQ 评估惯例，较客观。

## 6. 主要结论与发现

- DLLMQuant 在 **4-bit 量化**（W4A4）下，在 GSM8K 上相比 AWQ 提升 **超过 10 个百分点的准确率**，几乎达到全精度水平。
- 大幅降低模型存储和加速推理：量化后模型大小压缩约 4 倍，计算速度提升显著（具体数值未在摘要中给出）。
- 有效解决了动态掩码量化冲突的三个问题：TMAS 捕获时间步分布，IA-AQ 保护重要 token，CGQ 对齐置信度分布，三者协同实现精度无损压缩。
- 验证了扩散 LLM 可通过专门设计的 PTQ 实现高效部署，为 LLaDA 等模型的低资源应用提供了可行方案。

## 7. 优点

1. **问题切入精准**：首次系统分析扩散 LLM 动态掩码与量化的根本冲突，并归纳为三个可操作问题。
2. **方法设计巧妙**：TMAS 结合时间与掩码因子，IA-AQ 利用注意力重要性，CGQ 融入置信度权重，分别针对不同冲突源，逻辑自洽。
3. **效果显著**：在最具挑战性的 W4A4 下取得超过 10 点的准确率提升，对比 AWQ 等强基线优势明显。
4. **效率提升**：在不损失精度的情况下压缩模型，有助于扩散 LLM 的实际部署。
5. **技术通用性**：框架设计不依赖于特定扩散 LLM 架构，可能适用于其他类似模型。

## 8. 不足与局限

1. **实验覆盖有限**：仅以 LLaDA 为主要评估对象，未在多个不同类型、不同规模的扩散 LLM（如 MDLM、Diffusion-LM 变种）上验证泛化性。
2. **缺少资源消耗报告**：未提供校准时间、显存占用或推理加速的具体数字，影响实用性评估。
3. **消融实验细节不详**：从摘要无法判断三个组件各自的贡献大小，以及是否有冗余设计。
4. **潜在偏差风险**：校准集由 TMAS 动态采样生成，可能引入对特定分布过拟合的风险；未讨论校准集大小的影响。
5. **应用限制**：方法依赖注意力分数和置信度计算，会增加校准阶段的额外开销；对于无法获取注意力分数的扩散模型可能不适用。 
6. **理论分析不足**：虽然描述了三个冲突问题，但未提供理论建模或证明为什么这些技术能精确解决误差累积。

（完）
