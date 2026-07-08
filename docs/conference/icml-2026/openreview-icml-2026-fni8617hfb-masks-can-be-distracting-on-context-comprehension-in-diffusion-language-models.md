---
title: "Masks Can Be Distracting: On Context Comprehension in Diffusion Language Models"
title_zh: 掩码可能造成干扰：扩散语言模型的上下文理解研究
authors: "Julianna Piskorz, Cristina Pinneri, Alvaro Correia, Motasem Alfarra, Risheek Garrepalli, Christos Louizos"
date: 2026-04-30
pdf: "https://openreview.net/pdf/396594a603749acf53af948e136f7c26fe4c072f.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 掩码扩散语言模型的上下文理解能力分析
tldr: 该研究揭示了掩码扩散语言模型的两个关键局限：一是存在强烈的局部性偏好，即对输入中相关信息的敏感度取决于其位置；二是生成过程中附加的大量掩码令牌会显著降低上下文理解能力。这些发现指出了MDLMs在困惑度之外的重要缺陷。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 掩码扩散语言模型理论上应利用全局上下文，但其实际理解能力未知。
method: 设计实验系统分析MDLMs的上下文利用模式，发现局部性偏差和掩码令牌干扰。
result: MDLMs的上下文理解受限于局部性和掩码令牌数量，且性能对信息位置敏感。
conclusion: 为改进MDLMs的设计提供了重要洞见，提示需要缓解掩码带来的理解损失。
---

## Abstract
Masked Diffusion Language Models (MDLMs) have recently emerged as a promising alternative to Autoregressive Language Models (ARLMs), leveraging a denoising objective that, in principle, should enable more uniform context utilisation. In this work, we examine the context comprehension abilities of MDLMs and uncover two key limitations. First, despite their more global training objective and bidirectional attention mechanism, similarly to ARLMS, **MDLMs exhibit a strong locality bias**: performance is highly sensitive to the position of relevant information within the input, favouring local over distant context. Second, appending a large number of **mask tokens—required for generation—can significantly degrade context comprehension** in models trained from scratch. Through systematic ablations, we find that these masks **act as distractors**, reducing the model's ability to process relevant information.
  To address and further study this undesirable behaviour, we introduce the mask-agnostic loss function that encourages predictions to remain invariant to the number of appended masks. Fine-tuning with this objective substantially mitigates the distracting effect of masks, improving robustness of MDLMs.
  Overall, our findings reveal critical limitations of the current MDLM training paradigm, with implications for training, evaluation and deployment.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究背景**：掩码扩散语言模型（MDLM）作为自回归语言模型（ARLM）的替代方案，理论上应能更均匀地利用上下文信息，因其采用去噪训练目标和双向注意力机制。
- **核心问题**：MDLM 的实际上下文理解能力究竟如何？是否真如理论所期望的那样优于 ARLM？
- **整体含义**：该研究旨在揭示 MDLM 在上下文理解方面的关键局限性，为模型设计、训练和部署提供改进方向。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：通过系统实验诊断 MDLM 的上下文利用模式，发现其存在局部性偏见和掩码令牌干扰两个主要缺陷，并提出一种损失函数来缓解掩码带来的干扰。
- **关键技术细节**：
  - **局部性偏见分析**：设计实验，改变相关信息在输入序列中的位置，观察模型性能变化，证明模型偏好局部信息而非远处信息。
  - **掩码令牌干扰诊断**：在生成过程中，模型需要附加大量掩码令牌；研究发现这些掩码作为“分心物”降低了模型处理相关信息的能力。
  - **提出的方法——掩码无关损失函数（mask-agnostic loss）**：鼓励模型预测对附加掩码数量保持不变性，通过微调来减轻掩码的干扰效应，提升鲁棒性。

## 3. 实验设计：数据集、基准、对比方法

- **数据集**：摘要未明确提及具体数据集名称，但实验目的为测试上下文理解，推测使用了标准语言建模数据集（如 WikiText、BooksCorpus 等）。
- **基准（Benchmark）**：以自回归语言模型（ARLM）作为主要对比基线，比较上下文利用均匀性。
- **对比方法**：对比了标准 MDLM 训练范式、微调后的 MDLM（使用掩码无关损失）以及 ARLM，评估不同设置下的上下文理解性能。

## 4. 资源与算力

- 论文摘要及元数据中**未明确说明**所使用的算力（GPU 型号、数量、训练时长等）。因此无法提供具体数据，仅能指出作者未披露此类信息。

## 5. 实验数量与充分性

- **实验组数**：虽然未列出具体实验数量，但从摘要描述可知包含了以下实验：
  - 局部性偏差的定量分析（改变信息位置）。
  - 掩码令牌数量的消融实验（不同数量的附加掩码对性能的影响）。
  - 使用掩码无关损失进行微调前后的对比实验。
- **充分性评价**：实验设计较为系统，覆盖了主要假设的验证（局部性、掩码干扰）和提出的解决方案（掩码无关损失）。但由于缺少多数据集和多模型规模的对比，可能存在泛化性不足的风险。整体而言，实验在揭示缺陷方面是充分的，但消融实验的全面性有待全文确认。

## 6. 论文的主要结论与发现

- **结论一**：MDLM 表现出强烈的局部性偏见，性能高度依赖相关信息在输入中的位置，偏好近处信息而非远处信息，与 ARLM 类似。
- **结论二**：生成过程中附加的大量掩码令牌会显著降低模型上下文理解能力，掩码起到“分心物”的作用。
- **结论三**：通过引入掩码无关损失函数进行微调，可以有效缓解掩码的干扰，提升 MDLM 的鲁棒性。
- **总体发现**：当前 MDLM 训练范式存在关键缺陷，不仅限于困惑度等指标，在上下文理解方面有本质局限，对训练、评估和部署均有重要启示。

## 7. 优点：方法或实验设计上的亮点

- **新颖视角**：首次系统研究 MDLM 的上下文理解能力，而非仅关注困惑度或生成质量。
- **明确的缺陷诊断**：区分了局部性偏见和掩码干扰两个独立问题，提供了可操作的改进方向。
- **提出的损失函数简洁有效**：掩码无关损失概念清晰，通过微调即可见效，具有较强的实用价值。
- **实验设计具有针对性**：通过控制变量（位置、掩码数量）直接验证假设，结果可信度高。

## 8. 不足与局限

- **数据集覆盖有限**：未提及具体数据集和规模，可能仅在少量标准数据集上实验，缺乏对多样场景（如对话、长文本、代码等）的验证。
- **模型规模与变体单一**：未说明是否在多种大小的 MDLM 上验证，可能仅针对特定参数规模的模型，结论泛化性存疑。
- **与 ARLM 对比的不公平性风险**：虽然指出 MDLM 有局部性偏见，但未详细说明控制变量（如训练数据量、步数等）是否完全对齐。
- **未讨论计算开销**：缺失训练/推理算力信息，读者难以评估方法的实际成本。
- **应用限制**：研究主要关注缺陷诊断，未给出在所有场景下都能完全解决掩码干扰的完整方案，微调后的模型在长序列生成中的表现尚待进一步验证。

（完）
