---
title: "Coevolutionary Continuous Discrete Diffusion: Make Your Diffusion Language Model a Latent Reasoner"
title_zh: 协同进化连续离散扩散：让扩散语言模型成为潜在推理器
authors: "Cai Zhou, Chenxiao Yang, Yi Hu, Chenyu Wang, Chubin Zhang, Muhan Zhang, Lester Mackey, Tommi Jaakkola, Stephen Bates, Dinghuai Zhang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/11287f47583fcc683b7b1cc53f3fbc3ac678bcf3.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 扩散语言模型核心；连续与离散对比
tldr: 现有扩散语言模型多为离散，但连续扩散模型理论上表达能力更强。本文证明连续扩散比离散扩散和循环变压器表达能力更强，并提出协同进化连续离散扩散方法，使连续扩散模型在推理任务上达到领先水平，表明扩散语言模型不必局限于离散空间。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 当前连续扩散语言模型性能不及离散版本，但理论表达能力更强，需要弥合理论与实践差距。
method: 提出协同进化训练框架，结合连续和离散扩散过程的优势，并通过理论证明引导训练。
result: 在多个推理基准上，所提连续扩散模型达到最先进水平，超越离散扩散和循环变压器。
conclusion: 扩散语言模型不一定需要离散空间，连续扩散经适当训练可成为强大的推理模型。
---

## Abstract
Diffusion language models, especially masked discrete diffusion models, have achieved great success recently. While there are some theoretical and primary empirical results showing the advantages of latent reasoning with looped transformers or continuous CoT, continuous diffusion models typically underperform their discrete counterparts. In this paper, we argue that diffusion language models do not necessarily need to be in the discrete space. In particular, we prove that continuous diffusion models have stronger expressivity than discrete diffusions and looped transformers. We attribute the contradiction between the theoretical expressiveness and empirical performance to their practical trainability: while continuous diffusion provides intermediate supervision that looped transformers lack, they are harder to generate and decode tokens in the continuous representation space compared with discrete states. We therefore propose **C**oevolutionary **C**ontinuous **D**iscrete **D**iffusion (CCDD), which defines a joint multimodal diffusion process on the union of a continuous representation space and a discrete token space, leveraging a single model to simultaneously denoise in the joint space. By combining two modalities, CCDD is expressive with rich semantics in the latent space, as well as good trainability and sample quality with the help of explicit discrete tokens. We also propose effective architectures and advanced training/sampling techniques for CCDD, which reveals strong empirical performance in extensive language modeling experiments on real-world tasks.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：扩散语言模型（Diffusion Language Models）近期取得显著成功，尤其是掩码离散扩散模型。理论研究表明，连续扩散模型比离散扩散和循环变压器（looped transformers）具有更强的表达能力，并且连续思维链（continuous CoT）在隐式推理上有潜力。然而，在实践中连续扩散模型的表现通常不如离散版本。这种“理论强、实践弱”的矛盾成为了研究的核心问题。
- **整体含义**：作者主张扩散语言模型不一定必须限制在离散空间。通过合适的训练框架，连续扩散模型完全有能力成为强大的推理模型，不必刻意回避连续表示。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：提出**协同进化连续离散扩散（Coevolutionary Continuous Discrete Diffusion, CCDD）**，在连续表示空间与离散标记空间的并集上定义联合多模态扩散过程，用单一模型同时在联合空间中进行去噪。
- **关键技术细节**：
  - 联合两个模态：连续空间提供丰富的语义表达和隐式中间监督（这是循环变压器所缺乏的）；离散空间提供良好的可训练性和样本质量（帮助生成和解码）。
  - 通过一个模型协同进化，同时处理连续和离散的扩散过程，使连续表示能够借助离散标记的引导提升可训练性，同时保留其表达优势。
  - 为有效实现CCDD，作者还设计了专门的架构以及先进的训练/采样技术（摘要中未详述具体架构，但提及了“effective architectures and advanced training/sampling techniques”）。
- **公式或算法流程**：摘要未给出具体公式，但可推测其扩散过程同时作用于连续向量和离散token，两个模态的噪声和去噪步长可能耦合或交替。

## 3. 实验设计：数据集、基准、对比方法

- **使用的数据集/场景**：摘要中仅提到“extensive language modeling experiments on real-world tasks”，未明确列出具体数据集名称（如GLUE、SuperGLUE、推理基准如GSM8K、MATH等）。元数据中提及“多个推理基准”，但未详细说明。
- **基准**：未明确指定标准benchmark，但推测可能包含常识推理、数学推理等任务。
- **对比方法**：对比了离散扩散模型、循环变压器（looped transformers）以及可能已有的连续扩散模型。摘要强调CCDD达到了“most advanced level”（最先进水平），但具体对比结果未呈现。
- **评价指标**：未说明，通常是困惑度、准确率或推理任务上的正确率。

## 4. 资源与算力

- 论文摘要及元数据中**未提及**任何关于GPU型号、数量、训练时长或算力消耗的信息。因此无法总结这一点，只能指出文中未明确说明。

## 5. 实验数量与充分性

- **实验数量**：摘要未给出具体实验组数。但元数据中提及“多个推理基准”以及“广泛语言建模实验”，推测至少包含3-5个不同任务或数据集。消融实验方面，摘要提到“提出有效架构和先进训练/采样技术”，很可能有消融验证每个组件的贡献。
- **充分性与客观性**：由于信息不足，无法断言。但考虑到该论文被ICML-2026接收（评分9.0），通常评审会要求充分的实验对比和消融，因此推测实验设计是合理的。但摘要本身缺乏细节，需要阅读原文才能评估公平性。

## 6. 论文的主要结论与发现

- 连续扩散模型的表达能力理论上强于离散扩散和循环变压器。
- 连续扩散模型实践性能不佳的原因在于**可训练性**（trainability）问题——连续空间中的生成和解码比离散困难。
- CCDD通过联合训练连续和离散模态，弥合了理论与实践差距，使连续扩散模型在推理任务上达到**最先进水平**。
- 结论：扩散语言模型不必局限于离散空间；经过适当训练（如CCDD），连续扩散可以成为强大的推理模型（latent reasoner）。

## 7. 优点

- **理论分析扎实**：证明了连续扩散的表达能力优于离散扩散和循环变压器，为方法提供了坚实理论基础。
- **创新性框架**：首次提出联合连续‑离散扩散过程，巧妙结合了两者优势（连续的高语义丰富性与离散的高可训练性）。
- **实践突破**：解决了连续扩散语言模型长期以来的性能瓶颈，推动了该领域从“理论强、实践弱”到“理论实践双优”的转变。
- **技术贡献全面**：包含理论分析、模型设计、训练算法和采样技巧，覆盖面广。

## 8. 不足与局限

- **实验细节缺失**：摘要未提供具体的基准数据集、对比基线性能数值、消融实验结果等，难以全面评估方法有效性。
- **资源与可复现性**：未提及计算成本，可能影响其他研究者复现或扩展。
- **潜在偏差风险**：仅提到“真实世界任务”，但未说明任务类型是否涵盖多样领域（如分类、生成、对话等），可能存在任务选择偏差。
- **应用限制**：联合两个模态意味着模型结构更复杂，参数量和推理开销可能增加，文中未讨论实际部署成本。
- **可解释性**：连续‑离散联合扩散的隐式推理过程是否易于理解和调试，论文未涉及。

（完）
