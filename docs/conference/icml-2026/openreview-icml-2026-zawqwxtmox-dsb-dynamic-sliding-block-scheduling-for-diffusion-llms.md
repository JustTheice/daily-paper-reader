---
title: "DSB: Dynamic Sliding Block Scheduling for Diffusion LLMs"
title_zh: DSB：面向扩散大语言模型的动态滑动块调度
authors: "Lizhuo Luo, Shenggui Li, Yonggang Wen, Tianwei Zhang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/8611761a23c91ef0e3f0d4f02387ce30ad88badd.pdf"
tags: ["query:diffusion-lm"]
score: 8.0
evidence: 针对扩散大语言模型的动态块调度
tldr: 针对扩散语言模型在推理时固定块调度忽视语义难度导致次优的问题，提出动态滑动块调度（DSB），能根据语义难度动态调整块大小，避免对不确定位置过早承诺并使简单位置延迟。实验表明DSB在生成质量和推理效率上均优于固定调度策略。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有扩散LLM的固定块调度与语义难度无关，导致生成质量与效率的权衡不佳。
method: 提出动态滑动块调度（DSB），根据语义难度自适应调整块大小，实现更可靠高效的推理。
result: 实验证明DSB在文本生成质量和速度上均超越固定块基线。
conclusion: 动态调度适用于扩散语言模型的并行解码，是一种无需额外训练的改进策略。
---

## Abstract
Diffusion large language models (dLLMs) have emerged as a promising alternative for text generation, distinguished by their native support for parallel decoding. In practice, block inference is crucial for avoiding order misalignment in global bidirectional decoding and improving output quality. However, the widely-used fixed, predefined block (naive) schedule is agnostic to semantic difficulty, making it a suboptimal strategy for both quality and efficiency: it can force premature commitments to uncertain positions while delaying easy positions near block boundaries. In this work, we analyze the limitations of naive block scheduling and disclose the importance of dynamically adapting the schedule to semantic difficulty for reliable and efficient inference. Motivated by this, we propose **Dynamic Sliding Block (DSB)**, a training-free block scheduling method that uses a sliding block with a dynamic size to overcome the rigidity of the naive block. To further improve efficiency, we introduce **DSB Cache**, a training-free KV-cache mechanism tailored to DSB. Extensive experiments across multiple models and benchmarks demonstrate that DSB, together with DSB Cache, consistently improves both generation quality and inference efficiency for dLLMs. Code is released at https://github.com/lizhuo-luo/DSB.

---

## 论文详细总结（自动生成）

# 中文总结：DSB：面向扩散大语言模型的动态滑动块调度

## 1. 核心问题与整体含义（研究动机与背景）
- **背景**：扩散大语言模型（dLLMs）因其原生支持并行解码，成为文本生成的有前途替代方案。实践中，块推理（block inference）对于避免全局双向解码中的顺序错乱、提升输出质量至关重要。
- **问题**：广泛使用的固定、预定义块调度（naive schedule）与语义难度无关，导致质量和效率上的次优：①迫使对不确定位置过早做出承诺；②使接近块边界的简单位置延迟处理。这种固定调度策略无法根据语义难度动态调整，限制了生成质量和推理效率的权衡。

## 2. 方法论：核心思想、关键技术细节
- **核心思想**：动态适应调度策略到语义难度，以实现更可靠、高效的推理。
- **方法名称**：**动态滑动块（Dynamic Sliding Block, DSB）**，一种无需训练的块调度方法。
- **关键技术细节**：
  - 使用一个**动态大小的滑动块**克服固定块的刚性问题。块大小根据当前生成的语义难度自适应调整，从而在不确定区域避免过早承诺，在确定区域加快解码。
  - 为了进一步提升效率，引入**DSB Cache**，一种针对DSB设计的无需训练的KV缓存机制。
- **算法流程（文字描述）**：在每一步解码中，基于当前上下文语义难度，动态计算下一个滑动块的大小；块向前滑动，每次只生成块内的部分token；同时利用DSB Cache避免重复计算，提高缓存利用率。无需额外训练，直接应用于现有dLLM。

## 3. 实验设计
- **数据集/场景**：论文未在提供的摘要中明确列出具体数据集，但提到在多个模型和基准（multiple models and benchmarks）上进行实验。推测包含标准文本生成基准（如语言建模、文本补全等）。
- **Benchmark**：使用多个扩散LLM模型和标准文本生成基准，与固定块（naive block）基线对比。
- **对比方法**：主要是与固定块调度（naive block schedule）进行比较，可能还包括其他调度策略（如固定块大小变体）。具体基线需查看全文。

## 4. 资源与算力
- 论文摘要及元数据中**未明确说明**使用的GPU型号、数量、训练时长等算力信息。仅提到方法无需训练（training-free），因此推理所需算力取决于具体模型和硬件。需查阅全文获取详细信息。

## 5. 实验数量与充分性
- **实验数量**：摘要中提及“extensive experiments across multiple models and benchmarks”，表明覆盖多个模型和基准数据集，但未给出具体数字。
- **充分性评估**：从摘要判断，实验涵盖了多个扩散LLM模型和标准基准，并与固定块基线对比，初步说明充分。但缺乏消融实验细节（如不同动态调度策略、缓存机制影响等），需全文确认是否涵盖。若仅作横向对比，则可能不够全面。总体上，实验设计有明确对比，但需更详细报告。

## 6. 主要结论与发现
- DSB（结合DSB Cache）在生成质量和推理效率上**一致超越**固定块基线，对于扩散LLM是一种更优的并行解码调度策略。
- 动态调度无需额外训练，即可实现更好的质量-效率权衡。
- 代码已开源，支持复现。

## 7. 优点
- **创新型**：首次针对扩散LLM的动态块调度，解决了固定块忽视语义难度的问题。
- **实用性**：无需训练，可直接应用于现有扩散LLM，降低使用门槛。
- **效率提升**：通过动态滑动块和专用KV缓存机制，同时提升质量和速度。
- **开放科学**：代码开源，便于社区验证和扩展。

## 8. 不足与局限
- **实验细节不足**：摘要未提供具体数据集、模型规模、实验设置、统计显著性等，需阅读全文确认。
- **方法通用性**：DSB仅针对扩散LLM的并行解码，能否推广到其他类型的扩散模型（如图像、音频）未讨论。
- **语义难度度量依赖**：动态块大小依赖语义难度的估计，该估计方法是否鲁棒、是否需要额外计算开销未在摘要说明。
- **应用限制**：仅适用于基于块的并行解码场景，对于串行扩散模型可能不适用。
- **算力报告缺失**：未提供推理速度、显存占用等定量比较，无法评估实际部署成本。

（完）
