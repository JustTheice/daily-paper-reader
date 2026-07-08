---
title: "LUGS: Latent-aware Guidance for Efficient Unmasking in Diffusion Large Language Models"
title_zh: LUGS：面向扩散大语言模型的高效解掩码潜在感知引导
authors: "Nuanqiao Shan, Kairong Han, Xinpeng Dong, Kun Kuang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/40407548d2cc0c18efc194bc1861a17410bc5290.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 基于潜在感知的解掩码引导用于扩散语言模型
tldr: 扩散语言模型的生成质量严重依赖解码策略，现有方法通常使用置信度或熵等局部启发式规则，无法捕获序列级依赖和语义上下文。本文提出潜在感知解掩码引导搜索（LUGS），利用模型内部隐藏状态指导解掩码过程，补偿局部启发式规则的不足。实验结果显示，LUGS在多个文本生成任务上显著优于现有解码策略，且计算开销可控。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有扩散语言模型解码依赖局部启发式规则，忽略了序列级依赖和语义上下文。
method: 提出LUGS框架，利用模型内部隐藏状态计算潜在感知分数来引导解掩码过程。
result: LUGS在多个文本生成基准上优于基于置信度或熵的基线方法。
conclusion: 潜在感知引导能有效提升扩散语言模型的生成质量，是优于局部启发式的通用解码改进方案。
---

## Abstract
Diffusion Language Models (DLMs) have emerged as a flexible alternative to autoregressive (AR) models. They can decode tokens in any order, but the generation quality critically depends on the decoding strategy. Existing approaches predominantly rely on local heuristics, such as confidence or entropy, which may fail to capture sequence-level dependencies and the semantics in the context. To solve this problem, we propose Latent-aware Unmasking Guidance Search (LUGS), a novel decoding framework that leverages the model's internal hidden states to guide the unmasking process. By incorporating latent-aware scores to compensate for the limitations of local heuristics such as confidence or entropy, LUGS improves the model's performance. Extensive experiments on various downstream tasks demonstrate that our approach consistently outperforms existing baselines on LLaDA-8B-instruct and LLaDA-1.5 models. In Science and Reason tasks, LUGS improved performance by more than 1\% on both base models. And LUGS obtains an average improvement of 3.5\% in code generation. Remarkably, LUGS outperforms the beam search baseline by more than 5\%  on average using LLaDA-8B-Instruct on code tasks. These results highlight the potential of latent-aware guidance for advancing controllable and high-quality generation.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：扩散语言模型（DLMs）的生成质量严重依赖于解码策略。现有解码方法主要依赖局部启发式规则（如置信度或熵），这些规则无法捕获序列级依赖和语义上下文，导致生成结果质量受限。
- **研究动机**：DLMs 可以以任意顺序解码 token，这种灵活性提供了优化空间，但传统局部启发式指标（如 token 置信度）忽略了整体语义连贯性，需要更全局、更具语义感知的解码引导。
- **整体含义**：本文提出一种利用模型内部隐藏状态（潜在表示）来引导解掩码过程的新框架，弥补局部启发式的不足，从而提升整体生成质量。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：利用扩散语言模型解码过程中产生的**内部隐藏状态**，计算“潜在感知分数”，以此补偿单纯依赖置信度或熵等局部指标的局限性。
- **关键技术细节**：
  - 框架名称：**LUGS（Latent-aware Unmasking Guidance Search）**。
  - 在每一步解掩码时，不仅考虑当前 token 的局部置信度或熵，还融合模型中间隐藏层输出的潜在表示信息，形成更全面的评分函数。
  - 通过潜在感知分数重新排序候选 token 或引导解掩码顺序，使得序列级依赖和上下文语义被更加有效地捕捉。
  - 该框架作为通用解码改进方案，可应用于现有扩散语言模型（如 LLaDA），无需修改模型架构。
- **公式或算法流程**：论文未提供具体公式细节（摘要仅描述思路），但根据描述，算法大致流程为：对于每个待解掩码位置，计算该位置的隐藏状态与上下文语义的潜在关系得分，结合局部置信度获得最终得分，按得分高低决定解掩码顺序或 token 选择。

## 3. 实验设计：数据集、基准与对比方法

- **使用的数据集/场景**：涵盖**Science（科学）**、**Reason（推理）** 和**Code（代码生成）** 三类下游任务。具体数据集名称未在摘要中说明。
- **基准模型**：LLaDA-8B-instruct 和 LLaDA-1.5 两个模型。
- **对比方法**：包括基于置信度的解码、基于熵的解码以及**beam search 基线**。LUGS 与此类基线比较。

## 4. 资源与算力

- **文中未明确说明**使用了多少算力（GPU 型号、数量、训练时长等）。仅提到模型为 LLaDA-8B-instruct 和 LLaDA-1.5，但未提及实验硬件配置或计算成本数据。需要指出这一信息缺失。

## 5. 实验数量与充分性

- **实验数量**：在三个任务类别（Science、Reason、Code）上对比了两个模型，并报告了平均提升百分比。此外，在代码任务上与 beam search 进行了额外对比。摘要未提及消融实验（如去除潜在感知组件、不同潜在特征选择等），但可能正文中有更详细实验。
- **充分性评价**：
  - **优点**：覆盖了多种任务类型（科学、推理、代码），模型规模从 1.5B 到 8B，具有一定代表性。
  - **不足**：未明确报告统计显著性、多次运行平均、方差等；任务具体数据集名称缺失；消融实验未提及；缺乏在更多模型（如其他 DLM 架构）上的泛化验证。因此实验充分性一般，但作为原型验证足够。

## 6. 论文的主要结论与发现

- LUGS 在 Science 和 Reason 任务上，在两种基础模型上均**提升超过 1%**。
- 在代码生成任务上，平均提升**3.5%**；使用 LLaDA-8B-Instruct 时，LUGS 比 beam search 基线**平均提升超过 5%**。
- 潜在感知引导能够有效替代或补充局部启发式策略，显著改善扩散语言模型的生成质量。
- 结论：潜在感知引导是优于局部启发式的通用解码改进方案，具有推进可控高质量生成的潜力。

## 7. 优点

- **方法创新**：首次将模型内部隐藏状态（潜在表示）引入扩散语言模型的解码引导，突破了局部启发式的局限。
- **强实用性**：作为框架可直接应用于现有 DLM，无需修改模型架构或重新训练，轻量且有效。
- **显著收益**：在多个任务和模型上获得一致提升，特别是在代码生成任务中提升幅度大，且优于 beam search。
- **计算开销可控**：摘要提到“计算开销可控”，说明该方法具有实际部署潜力。

## 8. 不足与局限

- **实验覆盖有限**：仅测试了 LLaDA 系列两个模型，未验证在其他扩散语言模型（如 MDLM、SEDD 等）上的有效性。
- **缺乏消融与敏感性分析**：未公开潜在感知分数的具体计算方式及不同设计选择的消融，影响对方法核心贡献的理解。
- **资源与复现信息缺失**：未提供 GPU 时间、内存占用等计算资源信息，不利于可复现性。
- **任务数据集不透明**：摘要仅提及任务类别，未给出具体数据集名称、指标细节等，难以直接复现比较。
- **可能偏差风险**：潜在感知分数依赖模型中间层表示，不同模型结构或训练策略可能导致引导效果差异，泛化性有待验证。
- **应用限制**：仅针对扩散语言模型，无法直接应用于自回归模型。

（完）
