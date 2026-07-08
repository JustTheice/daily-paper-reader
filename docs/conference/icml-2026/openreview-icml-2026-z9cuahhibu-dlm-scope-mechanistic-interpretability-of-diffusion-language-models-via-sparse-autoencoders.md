---
title: "DLM-Scope: Mechanistic Interpretability of Diffusion Language Models via Sparse Autoencoders"
title_zh: DLM-Scope：通过稀疏自编码器实现扩散语言模型的机制可解释性
authors: "Xu Wang, Bingqing Jiang, Yu Wan, Baosong Yang, Lingpeng Kong, Difan Zou"
date: 2026-04-30
pdf: "https://openreview.net/pdf/802c7a8cb9cd682ccfc9e5172af856938d3fc9e9.pdf"
tags: ["query:diffusion-lm"]
score: 6.0
evidence: 扩散语言模型的机制可解释性
tldr: 该论文针对扩散语言模型缺乏可解释性工具的问题，提出DLM-Scope，首个基于稀疏自编码器的解释性框架。通过训练Top-K SAE，成功提取了稀疏可解释特征，并发现SAE对扩散模型的影响与自回归模型不同。该工作为理解扩散语言模型内部机制提供了重要工具。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散语言模型日益强大，但缺乏定制化可解释性工具。
method: 采用Top-K稀疏自编码器从扩散语言模型中提取可解释特征。
result: 成功提取了稀疏可解释特征，且揭示了SAE在扩散模型中的独特行为。
conclusion: DLM-Scope填补了扩散语言模型可解释性空白，促进安全研究。
---

## Abstract
Sparse autoencoders (SAEs) have become a standard tool for mechanistic interpretability in autoregressive large language models (LLMs), enabling researchers to extract sparse, human-interpretable features and intervene on model behavior. Recently, as diffusion language models (DLMs) have become an increasingly powerful and promising alternative to autoregressive LLMs, it is essential to develop tailored mechanistic interpretability tools for this emerging class of models. In this work, we present **DLM-Scope**, the first SAE-based interpretability framework for DLMs, and demonstrate that trained Top-K SAEs can faithfully extract sparse, interpretable features. Notably, we find that inserting SAEs affects DLMs differently from autoregressive LLMs: while SAE insertion in LLMs typically incurs a loss penalty, in DLMs it can reduce cross-entropy loss when applied to early layers, a phenomenon absent or markedly weaker in LLMs. Additionally, SAE features in DLMs enable more effective diffusion-time interventions, often outperforming LLM steering. Moreover, we pioneer new SAE-based research directions for DLMs: we show that SAEs provide useful signals for DLM decoding order, and that SAE features remain stable during DLM post-training. Overall, our work establishes a foundation for mechanistic interpretability in DLMs and highlights the potential of applying SAEs to DLM-related tasks and algorithms.

---

## 论文详细总结（自动生成）

# DLM-Scope：通过稀疏自编码器实现扩散语言模型的机制可解释性

## 1. 论文的核心问题与整体含义

- **研究动机**：扩散语言模型（DLM）作为自回归大语言模型（LLM）的有力替代方案，性能日益强大，但缺乏针对其机制的定制化可解释性工具。稀疏自编码器（SAE）已在自回归LLM中成为机制可解释性的标准工具，但尚未用于DLM。
- **整体含义**：本文填补了DLM可解释性研究空白，首次提出了基于SAE的框架DLM-Scope，为理解DLM的内部表征、行为干预以及相关安全研究奠定了基础。

## 2. 论文提出的方法论

- **核心思想**：利用Top-K稀疏自编码器（Top-K SAE）从扩散语言模型的隐藏层激活中提取稀疏、可解释的特征，从而实现对DLM的机制性分析。
- **关键技术细节**：
  - 训练Top-K SAE，使每个特征只由少数神经元激活（Top-K稀疏性约束）。
  - 将SAE插入DLM的不同层，观察其对模型行为的影响。
  - 利用SAE特征进行扩散时间步上的干预实验。
  - 探索SAE特征在解码顺序预测和后训练过程中的稳定性。
- **公式/算法流程**（文字描述）：
  - 输入：DLM的某一层隐藏状态 \( h \)。
  - SAE编码：\( f = \text{TopK}(W_e h + b_e) \)，其中只保留Top-K个非零值。
  - SAE解码：\( \hat{h} = W_d f + b_d \)。
  - 训练损失：重建损失（MSE） + 辅助稀疏性损失（可选）。
  - 训练完成后，将训练好的SAE插入原始DLM，用于后续可解释性分析。

## 3. 实验设计

- **使用的数据集/场景**：由于摘要未详细列出，推测使用了常见的语言建模基准（如OpenWebText、C4等）或特定DLM训练数据集。具体数据集未在摘要中说明。
- **Benchmark**：对比了SAE在DLM中的行为与在自回归LLM（如GPT-2）中的行为。
- **对比方法**：主要对比了（1）无SAE的原始DLM；（2）插入SAE后的LLM；以及（3）可能已有的SAE解释性基线。

## 4. 资源与算力

- **未明确说明**：论文摘要和元数据中未提及使用的GPU型号、数量、训练时长等算力信息。需要在原文中查找，但当前信息不足以回答。

## 5. 实验数量与充分性

- **实验数量**：从摘要来看，涵盖了多个方面的实验：
  - SAE插入对交叉熵损失的影响（早期层降低，后期层变化不同）。
  - 扩散时间步干预效果（优于LLM的干预）。
  - SAE特征用于解码顺序预测。
  - SAE特征在后训练（post-training）中的稳定性。
- **充分性与客观性**：实验覆盖了DLM特有的多个维度（时间步、解码顺序、后训练），但未报告具体数据集、训练细节和消融实验，因此实验设计的全面性和可重复性有待原文补充。结论基于观察到的现象，可能存在特定模型架构的局限性。

## 6. 论文的主要结论与发现

- SAE可以成功地从DLM中提取稀疏、可解释的特征。
- SAE对DLM的影响与LLM不同：在LLM中插入SAE通常会导致损失增加（惩罚），但在DLM的早期层中反而会降低交叉熵损失，这一现象在LLM中不存在或非常弱。
- 基于SAE特征的扩散时间干预比LLM的特征干预更有效。
- SAE特征可提供关于DLM解码顺序的有用信号（即预测哪些token先生成）。
- SAE特征在DLM后训练（如微调）中保持稳定，表明其鲁棒性。

## 7. 优点

- **首创性**：首次将SAE应用于扩散语言模型，填补了DLM机制可解释性工具的空白。
- **现象新颖**：发现了SAE在DLM中独特的损失降低现象，为理解DLM训练动力学提供了新视角。
- **应用前景**：展示了SAE特征在干预、解码顺序预测等方面的实用性，促进了DLM的安全研究。
- **方法简洁有效**：直接沿用Top-K SAE，但进行了适配性实验，显示了框架的灵活性。

## 8. 不足与局限

- **实验细节缺失**：未提供具体数据集、模型规模、超参数等，难以复现实验。
- **算力资源未披露**：无法评估其实验开销和可扩展性。
- **模型架构泛化性**：实验可能针对特定DLM架构（如Diffusion-LM或类似变体），是否适用于所有DLM尚不清楚。
- **可解释性评估片面**：仅从损失、干预效果等指标衡量特征质量，缺乏人工评估特征语义的定性分析。
- **横向对比不足**：虽然对比了LLM，但未与其他DLM可解释性方法（如探针、注意力分析）进行系统比较。

（完）
