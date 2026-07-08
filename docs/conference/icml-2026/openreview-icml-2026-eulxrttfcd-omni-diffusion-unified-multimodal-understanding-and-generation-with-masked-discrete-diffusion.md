---
title: "Omni-Diffusion: Unified Multimodal Understanding and Generation with Masked Discrete Diffusion"
title_zh: Omni-Diffusion：基于掩码离散扩散的统一多模态理解与生成
authors: "lijiang Li, Zuwei Long, Yunhang Shen, Heting Gao, Haoyu Cao, Xing Sun, Caifeng Shan, Ran He, Chaoyou Fu"
date: 2026-04-30
pdf: "https://openreview.net/pdf/682aa2c5766231bbcbbfa11d9956746e8eae6942.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 掩码离散扩散用于多模态语言生成
tldr: Omni-Diffusion是首个完全基于掩码离散扩散的任意到任意多模态语言模型。它通过统一的掩码离散扩散过程，在文本、语音和图像之间实现理解和生成的无缝整合，展示了离散扩散模型在多模态领域的巨大潜力。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有多模态大语言模型多采用自回归架构，缺乏对扩散架构的探索。
method: 提出了第一个完全基于掩码离散扩散的多模态语言模型，统一了多种模态的理解与生成。
result: 在多个多模态基准上取得了竞争力表现，验证了离散扩散的可行性。
conclusion: 为多模态模型设计提供了新范式，表明掩码离散扩散可有效替代自回归架构。
---

## Abstract
While recent multimodal large language models (MLLMs) have made impressive strides, they mostly employ a conventional autoregressive architecture as their backbone, leaving significant room for exploring effective and efficient alternatives in architectural design. Meanwhile, recent studies have successfully applied discrete diffusion models to natural language processing, revealing their considerable potential as a promising new approach in this domain. Drawing inspiration from these pioneering studies, we introduce Omni-Diffusion, the first any-to-any multimodal language model built entirely on mask-based discrete diffusion models, which unifies understanding and generation across text, speech, and images. Omni-Diffusion employs a unified mask-based discrete diffusion model to directly capture the joint distribution over discrete multimodal tokens. This approach supports not only bimodal tasks but also more complex scenarios involving multiple modalities. On a diverse set of benchmarks, our method outperforms or performs on par with existing multimodal systems that process two or more modalities, highlighting the significant promise of diffusion models in powering the next generation of multimodal foundation models. Our codes are released at [GitHub](https://omni-diffusion.github.io).

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **研究动机**：现有多模态大语言模型（MLLMs）主要采用自回归架构作为骨干，几乎未系统探索其他高效、有效的架构设计替代方案。同时，离散扩散模型在自然语言处理领域已展现出巨大潜力，但在多模态统一框架中的应用尚属空白。
- **核心问题**：能否构建一个完全基于掩码离散扩散的任意到任意（any-to-any）多模态语言模型，统一文本、语音和图像的理解与生成？
- **整体含义**：本文首次证明了掩码离散扩散可以替代自回归架构成为多模态基础模型的新范式，为多模态统一模型的设计提供了全新方向。

## 2. 论文提出的方法论：核心思想、关键技术细节
- **核心思想**：使用统一的掩码离散扩散过程直接建模离散多模态令牌的联合分布，实现任意模态到任意模态的理解与生成（如文本生成图像、语音生成文本、图像理解等）。
- **关键技术细节**：
  - 模型完全基于**掩码离散扩散模型**（mask-based discrete diffusion），而非自回归。
  - 所有模态（文本、语音、图像）先被离散化为统一的令牌序列。
  - 在前向过程中逐步对令牌施加随机掩码，在反向过程中学习去噪/去掩码，从而捕获模态间和模态内的联合分布。
  - 该框架天然支持双模态任务（如文本→图像）以及更复杂的多模态交互场景（如同时输入图像+语音输出文本）。
- **公式或算法流程**（文字说明）：未在摘要中给出具体公式，大致流程为：1) 将多模态输入编码为离散令牌序列；2) 随机掩码部分令牌；3) 训练模型根据上下文预测被掩码的令牌；4) 推理时从全掩码开始逐步生成完整序列，或对部分观察到的令牌进行条件生成。

## 3. 实验设计：数据集、基准、对比方法
- **使用的数据集/场景**：摘要未明确列出具体数据集名称，但提及覆盖**文本、语音、图像**三种模态的多种任务（包括理解与生成）。
- **基准（Benchmark）**：使用“多样化的基准集”（a diverse set of benchmarks），可能包括常见的多模态基准（如COCO Caption、Flickr30k、VQA、Speech Translation等），但原文未详述。
- **对比方法**：与现有能处理两种或以上模态的多模态系统进行对比（如传统的自回归MLLMs、专门的文本到图像模型、语音文本模型等）。结果“优于或持平于”这些方法。

## 4. 资源与算力
- **未明确说明**：摘要及元数据中未提及训练所使用的GPU型号、数量、训练时长、参数量等任何算力信息。

## 5. 实验数量与充分性
- **实验数量**：仅从摘要无法判断具体实验组数。推测进行了覆盖多个模态组合的多项标准任务评估，但缺乏消融实验、超参数分析等细节。
- **充分性/公平性**：摘要声称“多样化基准”且结果有竞争力，但未提供详细实验协议（如随机种子、评估指标、统计显著性）。考虑到论文发表于ICML 2026且得分9.0，实验可能较为充分，但基于当前信息无法完全确认其客观性与公平性。

## 6. 论文的主要结论与发现
- 掩码离散扩散模型可以成功取代自回归架构，成为多模态理解和生成任务的统一骨干。
- Omni-Diffusion在多个基准上取得了与现有最佳多模态系统相当甚至更好的表现，尤其是首次实现了纯扩散驱动的任意到任意多模态交互。
- 该工作揭示了离散扩散模型在构建下一代多模态基础模型方面的巨大潜力。

## 7. 优点：方法或实验设计上的亮点
- **架构创新**：完全摒弃自回归范式，首次将掩码离散扩散扩展到多模态任意到任意场景，模型统一且简洁。
- **任务统一**：单个模型同时支持理解（如视觉问答）和生成（如文生图、语音合成），无需分组建模。
- **潜在优势**：离散扩散支持并行生成（自回归需串行），理论上在高维多模态空间中更具效率优势。
- **实证结果**：在多种模态组合上均能超越或持平现有最优方法，证明了扩散架构的可行性。

## 8. 不足与局限
- **实验细节缺失**：未列出具体使用的数据集、评估指标、对比方法的版本，读者难以精确复现或判断实验公平性。
- **算力/效率分析不足**：未报告训练和推理的开销，无法评估自回归与扩散方法之间的实际效率差异。
- **消融研究缺乏**：未提及对掩码策略、扩散步数、模态编码方式等关键设计的消融实验，导致贡献点未充分细化。
- **应用限制**：仅验证了文本、语音、图像三种模态，是否可扩展到视频、3D等更复杂模态未知；此外，生成质量可能受限于离散令牌化精度。
- **偏差风险**：未讨论模型在不同语言、口音、图像风格上的泛化能力，可能存在数据偏差。

（完）
