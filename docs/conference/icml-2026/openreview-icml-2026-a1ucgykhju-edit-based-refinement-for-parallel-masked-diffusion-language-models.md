---
title: Edit-Based Refinement for Parallel Masked Diffusion Language Models
title_zh: 并行掩蔽扩散语言模型的基于编辑的细化
authors: "Houxing Ren, Mingjie Zhan, Zimu Lu, Ke Wang, Yunqiao Yang, Haotian Hou, Junting Pan, Hongsheng Li"
date: 2026-04-30
pdf: "https://openreview.net/pdf/94d2fa5d0fc2b2e38c5c26192d5caa88df3f05bd.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 掩蔽扩散语言模型；并行解码；基于编辑的细化
tldr: 掩蔽扩散语言模型并行生成多个令牌时质量下降。本文提出ME-DLM，首先生成完整回答，然后通过轻量级编辑（替换、删除、插入）进行细化，编辑距离作为训练信号。实验表明该方法有效提升并行生成的一致性。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 并行生成时令牌独立性假设被违反导致一致性差。
method: 在初始生成后，用编辑操作（替换、删除、插入）细化序列，以编辑距离为监督。
result: 在文本生成任务上，ME-DLM提升了并行解码质量，接近自回归水平。
conclusion: 后编辑细化是改善掩蔽扩散并行生成的有效手段。
---

## Abstract
Masked diffusion language models enable parallel token generation and offer improved decoding efficiency over autoregressive models. However, their performance degrades significantly when generating multiple tokens simultaneously, due to a mismatch between token-level training objectives and joint sequence consistency. In this paper, we propose ME-DLM, an edit-based refinement framework that augments diffusion generation with lightweight post-editing steps. After producing an initial complete response, the model refines it through minimal edit operations, including replacement, deletion, and insertion, conditioned on the full sequence. Training supervision is derived from edit distance, providing a deterministic signal under a fixed canonicalization scheme for learning minimal corrections. This approach encourages sequence-level consistency through globally conditioned edits while preserving the efficiency benefits of parallel diffusion decoding. Extensive experiments demonstrate that ME-DLM improves the quality and robustness of multi-token parallel generation. In particular, when built upon LLaDA, our method achieves consistent gains of 11.6 points on HumanEval and 33.6 points on GSM8K while using one-eighth of the total diffusion steps. Code is available at https://github.com/renhouxing/ME-DLM.

---

## 论文详细总结（自动生成）

# 论文总结：Edit-Based Refinement for Parallel Masked Diffusion Language Models (ME-DLM)

## 1. 核心问题与整体含义
- **研究动机**：掩蔽扩散语言模型（Masked Diffusion Language Models）支持并行生成多个token，解码效率优于自回归模型。但当同时生成多个token时，由于**token级训练目标与序列级一致性之间存在不匹配**，生成质量显著下降。
- **整体含义**：该论文旨在**通过轻量级后编辑细化**，在不牺牲并行解码效率的前提下，提升多token并行生成的质量和一致性，使其接近自回归水平。

## 2. 方法论
- **核心思想**：在初始完整响应生成后，引入**基于编辑的细化步骤**，通过最小编辑操作（替换、删除、插入）对序列进行修正，从而增强序列级一致性。
- **关键技术细节**：
  - 编辑操作以**完整序列为条件**（globally conditioned），实现全局感知的修正。
  - 训练监督信号来源于**编辑距离**，在固定的规范化方案（canonicalization scheme）下提供确定性信号，用于学习最小修正。
  - 该方法保持并行扩散解码的效率优势，仅增加轻量级后处理步骤。
- **算法流程（文字说明）**：
  1. 使用掩蔽扩散模型并行生成一个完整的初始回答序列。
  2. 将初始序列与目标序列（或参考序列）对齐，计算编辑距离并确定最小编辑序列（替换、删除、插入的位置及内容）。
  3. 训练一个细化模块，以当前序列和条件信息为输入，输出编辑操作的概率分布。
  4. 在推理时，对初始生成序列应用预测的编辑操作，得到细化后的最终序列。

## 3. 实验设计
- **数据集/场景**：
  - **HumanEval**（代码生成任务）
  - **GSM8K**（数学推理任务）
- **基准（Benchmark）**：以 **LLaDA** 为基础模型构建 ME-DLM，并与其原始版本对比。
- **对比方法**：主要对比原始LLaDA（未使用后编辑细化），未提及与其他后编辑或细化方法的比较。消融实验可能包括不同编辑操作、编辑步数等（但摘要未详细列出）。

## 4. 资源与算力
- **未明确说明**：论文摘要及元数据中未提及使用的GPU型号、数量、训练时长等具体算力信息。读者需参考原文全文以获取更详细资源消耗。

## 5. 实验数量与充分性
- **实验数量**：主要报告了两个基准任务（HumanEval、GSM8K）的定量结果。此外，作者称进行了“extensive experiments”，但摘要中未列出更多的数据集或变体实验。可能包含消融研究（如编辑操作类型、步数影响等），但信息有限。
- **充分性评价**：
  - **优点**：在代码生成和数学推理两个具有代表性的任务上均取得显著提升（11.6和33.6个点），且仅用1/8扩散步数，验证了方法的有效性。
  - **局限性**：任务类型单一（主要针对编程和数学），未在自然语言生成、机器翻译等更广泛的文本生成任务上验证。对比基线较少，缺乏与其它后编辑或自回归方法的全面比较。总体而言，实验设计尚可但覆盖范围有限。

## 6. 主要结论与发现
- ME-DLM显著提升了并行掩蔽扩散模型的多token生成质量，在HumanEval上提升11.6个点，在GSM8K上提升33.6个点（基于LLaDA）。
- 这些提升仅需使用原始LLaDA总扩散步数的1/8，保持了高效性。
- 后编辑细化是一种简洁有效的方法，能够弥合token级训练与序列级一致性之间的差距，使并行生成质量接近自回归水平。

## 7. 优点
- **方法简单实用**：基于编辑的细化策略轻量且易于集成到现有扩散模型中，无需改变原始解码流程。
- **监督信号明确**：利用编辑距离作为确定性训练信号，避免了复杂的强化学习或对抗训练。
- **效率与质量平衡**：在提升质量的同时仅增加少量计算开销（后编辑步数很少），维持并行解码的高吞吐量。
- **效果显著**：在两个困难基准上取得大幅提升，验证了后编辑对并行生成一致性的重要性。

## 8. 不足与局限
- **实验覆盖范围有限**：仅测试了代码生成和数学推理任务，未涉及开放式文本生成、故事生成、对话等更依赖语言多样性的场景，泛化能力待验证。
- **对比基线不足**：未与自回归模型、其他扩散模型变体、或基于重写的后处理方法（如掩码预测、精调）直接比较，难以评估方法相对优势。
- **编辑操作的设计细节**：论文摘要未说明替换、删除、插入的具体实现方式（如何确定编辑位置、编辑数量等），这些超参数可能影响结果稳定性。
- **偏差风险**：编辑距离作为训练信号可能倾向于学习训练数据中的高频修正模式，导致对分布外输入的鲁棒性下降。
- **应用限制**：后编辑步骤仍需要额外的模型推理（细化模块），对于极低延迟要求的场景可能仍存在开销；且编辑操作可能引入新的错误或破坏原有合理内容。

（完）
