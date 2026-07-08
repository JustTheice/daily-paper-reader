---
title: Diffusion Language Models Are Natively Length-Aware
title_zh: 扩散语言模型天生具备长度感知能力
authors: "Vittorio Rossi, Giacomo Cirò, Davide Beltrame, Luca Gandolfi, Paul Röttger, Dirk Hovy"
date: 2026-01-23
pdf: "https://openreview.net/pdf/778befe6a317b8e23f3d35145655fc9f1589b5a3.pdf"
tags: ["query:diffusion-lm"]
score: 7.0
evidence: 扩散语言模型的长度感知裁剪
tldr: 观察到扩散语言模型（DLM）的潜在提示表征蕴含输出长度信息，提出零样本裁剪机制：在生成前动态截断上下文窗口至合适长度，从而减少无效扩散步数。实验表明该方法在不牺牲质量的前提下大幅降低了短响应的计算开销。
source: ICML-2026-Rejected-Public
selection_source: conference_retrieval
motivation: DLM对固定最大长度窗口进行等量扩散步数，短响应存在大量计算浪费。
method: 提出零样本动态窗口裁剪方法，利用提示表征估计输出长度，在推理前缩短上下文窗口。
result: 在多个对话和推理任务上，该方法显著减少浮点运算量，同时生成质量持平。
conclusion: 利用DLM的长度感知能力可低成本提升推理效率。
---

## Abstract
Unlike auto-regressive language models, which terminate variable-length generation upon predicting an End-of-Sequence (*EoS*) token, Diffusion Language Models (DLMs) operate over a fixed maximum-length context window for a predetermined number of denoising steps. However, this process is independent of the required response length, resulting in computational waste for the majority of short responses common in reasoning and chat tasks.
To address this problem, we conjecture that the latent prompt representation contains sufficient information to estimate the required output length. We provide empirical evidence for this phenomenon and propose a zero-shot mechanism to dynamically crop the context window before generation begins, leading to fewer diffusion steps and substantial computational savings.
We evaluate our approach on four benchmarks with diverse tasks---*GSM8K* (reasoning), *HumanEval* (code generation), *IfEval* (instruction following), and *LongFormQA* (question answering)---revealing massive efficiency gains at minimal performance impact. We report significant reductions in FLOPS across all tasks, with no statistically significant performance degradation, and significant performance improvements in 2 out of 4 tasks.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **问题**：自回归语言模型通过预测结束符（EoS）实现变长生成，而扩散语言模型（DLM）在固定的最大长度上下文窗口内执行固定步数的去噪过程，与所需输出长度无关。这导致大量短响应（如推理、对话任务中常见）存在显著的计算浪费。
- **动机**：为了降低DLM在短响应场景下的推理计算开销，同时保持生成质量。
- **整体含义**：论文发现DLM的潜在提示表征蕴含输出长度信息，并利用这一特性提出了零样本、无需额外训练的上下文窗口动态裁剪机制，在多个任务上实现了显著的效率提升且不影响性能。

### 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：在生成开始前，利用提示的潜在表征（latent prompt representation）估计输出长度，据此动态截断上下文窗口，从而减少无效的去噪步数和计算量。
- **关键技术细节**：
  - 假设：潜在提示表征中编码了所需输出长度的信息。
  - 零样本机制：无需微调或额外数据，直接基于已有DLM的提示表征进行长度估计。
  - 裁剪过程：在推理阶段，根据估计长度将固定最大窗口裁剪为更短的窗口，模型仅在该裁剪后的窗口内执行等量去噪步数（实际步数与窗口长度成正比？论文未明确，但推论是缩短窗口后步数可减少）。
- **公式或算法流程**（文字说明）：
  1. 输入提示，通过DLM的编码器得到潜在表征。
  2. 从该表征中提取长度估计值（具体估计方式未在摘要中详述，可能是通过学习一个简单映射器或基于隐空间度量）。
  3. 根据估计长度，将原始的固定最大上下文窗口裁剪为相应长度。
  4. 在裁剪后的窗口内运行标准去噪过程（步数不变或根据长度调整？论文称“leading to fewer diffusion steps”，暗示可能步数也按比例减少）。
  5. 输出生成结果。

### 3. 实验设计：数据集、基准、对比方法

- **数据集与场景**：四个覆盖不同任务的基准数据集：
  - **GSM8K**（数学推理）
  - **HumanEval**（代码生成）
  - **IfEval**（指令跟随）
  - **LongFormQA**（问答）
- **基准（Benchmark）**：未明确说明基线，推测是标准DLM模型（如MDLM、SSD-LM等）在固定最大窗口下的原样生成。
- **对比方法**：论文未列出明确的对比方法，但本质上是将“无裁剪的原始DLM”与“提出裁剪方法的DLM”进行对比（消融裁剪策略）。可能有与随机裁剪或固定长度裁剪的对比，但摘要未提及。

### 4. 资源与算力

- 论文摘要和元数据**未明确说明**使用的GPU型号、数量或训练时长。实验应为推理阶段评估，无需额外训练，因此计算开销主要体现在评估时。具体硬件环境未知。

### 5. 实验数量与充分性

- **实验数量**：在4个数据集上进行了主要实验。元数据未提及消融实验或超参数分析，但可能包含对长度估计精度的验证（有“提供经验证据”的描述）。
- **充分性**：覆盖了推理、代码、指令跟随和问答四个典型领域，多样性较好。但缺乏对长响应任务的评估、不同长度截断策略的对比、以及长度估计方法的消融研究。整体实验**相对充分**，但若缺少消融实验和统计细节则严谨性可增强。
- **公平性**：报告了所有任务上的FLOPS减少和性能变化，并指出无统计显著下降，在2/4任务上性能还有提升。但未说明是否控制了其他变量（如步数、随机种子），公平性基本可接受。

### 6. 论文的主要结论与发现

- DLM的潜在表示天然蕴含输出长度信息，可被利用。
- 提出的零样本动态窗口裁剪方法在**所有四个任务上**显著减少FLOPS（计算量降低）。
- **性能**：没有统计显著的性能下降，并且在GSM8K和HumanEval等2个任务上表现有统计显著的提升。
- 该方法是一种低成本、高效率的推理加速技术，无需改变模型架构或训练过程。

### 7. 优点：方法或实验设计上的亮点

- **零样本**：无需额外训练或标注数据，直接应用于预训练DLM。
- **高效**：显著降低推理计算量，尤其对短响应任务友好。
- **性能无损甚至提升**：裁剪可能去除了冗余计算，使模型更关注关键信息，导致部分任务性能改善。
- **通用性**：实验覆盖四种不同性质的任务，表明方法具有一定的泛化能力。

### 8. 不足与局限

- **实验覆盖局限**：仅使用了4个基准，且未评估长响应任务（如长篇文本生成），在短响应场景的优势可能无法推广到长输出。
- **偏差风险**：长度估计依赖潜在表征，若表征受噪声或提示长度影响，估计可能不准确，导致裁剪过短或过长影响质量。
- **应用限制**：方法假设DLM的隐藏表征可被直接用于长度预测，但不同DLM架构（如离散扩散、连续扩散）的表征特性可能不同，泛化性需要验证。
- **缺少细节**：未提供长度估计的具体算法、裁剪如何与去噪步数联动、以及步数减少的实际比例。
- **可重复性**：论文无公开代码或模型权重（仅摘要信息），限制了其他研究者复现。

（完）
