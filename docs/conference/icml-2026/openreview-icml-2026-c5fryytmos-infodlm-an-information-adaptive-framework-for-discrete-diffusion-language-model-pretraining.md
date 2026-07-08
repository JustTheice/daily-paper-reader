---
title: "InfoDLM: an Information-Adaptive Framework for Discrete Diffusion Language Model Pretraining"
title_zh: InfoDLM：面向离散扩散语言模型预训练的信息自适应框架
authors: "Shirou Jing, Chunshu Wu, Chuan Liu, Arghavan Bahadorinejad, Feitong Qiao, Dongfang Liu, Tony Geng"
date: 2026-04-30
pdf: "https://openreview.net/pdf/6e3a152f6cdb245d60d9a040f43eae7ac90121a5.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 离散扩散语言模型的自适应预训练框架
tldr: 该论文针对离散扩散语言模型预训练使用随机掩码导致计算浪费的问题，提出InfoDLM自适应框架。该框架将掩码选择建模为主动反馈过程，通过可训练信息增益信号量化每个掩码配置的信息量，并优先掩码信息量高的标记。实验表明InfoDLM提高了预训练效率，在理解和推理任务上达到更优性能。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有离散扩散语言模型预训练使用随机掩码，未能针对信息量大的标记，效率低。
method: 提出可训练信息增益信号，主动选择信息量高的标记进行掩码。
result: InfoDLM提升了预训练效率和下游任务性能。
conclusion: 自适应掩码选择可显著改善离散扩散语言模型预训练。
---

## Abstract
Diffusion language models (DLMs) can match or surpass similarly sized autoregressive language models on language understanding and reasoning. However, their mask-and-denoise pretraining relies on heuristic random masking, which fails to target the most informative tokens. Consequently, the model spends significant computational effort on redundant or trivial tokens. To address this, we propose InfoDLM, an adaptive DLM pretraining framework that reformulates mask selection as an active, feedback-driven process. InfoDLM targets tokens that offer the highest measurable information gain during mask selection. Specifically, we: (1) introduce a Trainable Information-Gain (TIG) signal to quantify information gain of each masking configuration; (2) develop a feedback mechanism that adapts the masking policy to the model’s evolving state with a maturity indicator; and (3) jointly optimize the DLM and masking policy through an interleaved training flow with minimal computational overhead. Across reasoning-oriented benchmarks, InfoDLM achieves up to 13\% improvement in reasoning accuracy over a small variant of LLaDA under comparable pretraining budgets.

---

## 论文详细总结（自动生成）

好的，以下是根据提供的论文信息生成的结构化中文总结。

# InfoDLM：面向离散扩散语言模型预训练的信息自适应框架 — 中文总结

## 1. 核心问题与整体含义（研究动机与背景）
- **问题**：现有离散扩散语言模型（DLM）的“掩码-去噪”预训练过程采用启发式的随机掩码策略，无法有效针对信息量大的标记（tokens）进行掩码。
- **后果**：模型在冗余或无意义的标记上花费大量计算资源，导致预训练效率低下，影响下游任务性能。
- **整体含义**：本文旨在通过自适应掩码选择来替代随机掩码，使DLM预训练更高效、更智能，从而在同等计算预算下获得更好的语言理解与推理能力。

## 2. 提出的方法论
- **核心思想**：将掩码选择重新建模为一个**主动的、由反馈驱动的过程**，优先掩码那些能带来最高可量化信息增益的标记。
- **关键技术细节**：
    - **可训练信息增益信号（TIG）**：引入一个可训练的模块，用于量化每种掩码配置的信息增益。
    - **反馈机制**：根据模型的当前状态（通过成熟度指标衡量），自适应地调整掩码策略。随着模型训练推进，掩码选择策略动态演化。
    - **联合优化**：通过交织的训练流程，同时优化扩散语言模型（DLM）和掩码策略（掩码选择器），且仅带来极小的额外计算开销。
- **算法流程（文字说明）**：
    1. 在预训练的每一步，计算当前模型状态下所有可能掩码组合的TIG值。
    2. 根据TIG值选择信息增益最高的标记进行掩码。
    3. 将掩码后的文本输入DLM进行去噪训练。
    4. 利用模型学习状态（成熟度）反馈更新掩码策略。
    5. 交替更新DLM参数和TIG模块参数，实现端到端优化。

## 3. 实验设计
- **数据集/场景**：面向推理的基准测试（reasoning-oriented benchmarks），具体数据集名称未在摘要中列出。
- **基准方法**：作者与**LLaDA的小变体**（small variant）进行对比，两者采用相似的预训练预算。
- **对比方法**：传统随机掩码的DLM预训练（即LLaDA的小变体作为基线）。

## 4. 资源与算力
- **说明**：论文摘要及提供的元数据中**未明确说明**所使用的GPU型号、数量及训练总时长。仅在结论中提到“comparable pretraining budgets”（可比的预训练预算），但未给出具体数值。

## 5. 实验数量与充分性
- **实验数量**：基于现有信息，仅报告了一项主要结果（推理准确率提升13%）。元数据未提及消融实验、不同模型规模或多数据集的结果。
- **充分性评价**：受限于信息源（仅摘要），无法全面评估。但考虑到本文被ICML 2026接收，正式论文中应当包含更详实的实验：如不同数据集（GLUE, SuperGLUE等）、不同规模模型、与更多基线（如BERT, AR-LM）对比、掩码选择效果的消融实验等。当前信息下的实验覆盖**不够充分**，但推测正式全文更为完整。**公平性**方面，与LLaDA在类似预算下对比，设计合理，但需要更多基线确保结论客观。

## 6. 主要结论与发现
- **主要结论**：InfoDLM通过自适应掩码选择显著改善了离散扩散语言模型的预训练效率与效果。
- **具体发现**：在推理导向的基准上，相比使用随机掩码的LLaDA小变体，InfoDLM在**推理准确率上提升高达13%**（under comparable pretraining budgets）。

## 7. 优点
- **方法论亮点**：
    - 将掩码选择从“被动随机”提升到“主动信息增益驱动”，更符合模型学习需求。
    - 引入了可训练的信息增益信号（TIG）和自适应反馈机制，使策略随模型状态动态演进，具有自适应性。
    - 联合优化设计使得额外计算开销极低，具有实际部署价值。
- **实验设计亮点**：
    - 在同一预算下与强基线（LLaDA）对比，验证了效率提升。
    - 聚焦推理任务，揭示该方法对复杂语言理解的增强作用。

## 8. 不足与局限
- **实验覆盖有限**：仅报告了推理基准上的单一指标，缺少语言理解（如GLUE）、文本生成、翻译等任务的验证，泛化能力不明。
- **资源细节缺失**：未提供算力消耗的具体信息，难以复现和量化效率提升。
- **潜在风险**：
    - TIG信号可能引入额外的过拟合风险（特别是如果信息增益信号本身不够鲁棒）。
    - 反馈机制依赖于模型成熟度指标的设计，该指标的选择可能影响性能。
- **应用限制**：目前仅针对离散扩散语言模型，是否适用于连续扩散模型或其他序列生成范式尚不清楚。

（完）
