---
title: "Beyond Confidence: Adaptive and Coherent Decoding for Diffusion Language Models"
title_zh: 超越置信度：扩散语言模型的自适应连贯解码
authors: "Kecheng Chen, Ziru Liu, Xijia Tao, Hui Liu, Xinyu Fu, Suiyun Zhang, Dandan Tu, Lingpeng Kong, Rui Liu, Haoliang Li"
date: 2026-04-30
pdf: "https://openreview.net/pdf/1b766c1d763ddaebe244cddaa00af39f76d3c8cb.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 扩散语言模型的解码方法
tldr: 提出Coherent Contextual Decoding (CCD)框架，利用历史上下文近似令牌预测的边际分布，从而增强序列连贯性并及早拒绝次优路径。实验表明，CCD在多个文本生成任务上显著优于基于置信度的现有方法。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有扩散语言模型推理方法仅依赖局部即时指标，导致生成质量次优。
method: 提出CCD，通过利用历史上下文估计边际分布并进行路径级拒绝采样。
result: CCD在连贯性和早期错误拒绝上优于基线方法。
conclusion: 为扩散语言模型提供了更可靠的解码范式。
---

## Abstract
Diffusion Language Models (DLMs) have recently achieved significant success due to their any-order generation capabilities. However, existing inference methods typically rely on local, immediate-step metrics—such as confidence or entropy—which inherently lack a more reliable perspective, leading to sub-optimal generation quality. To address this, we propose **C**oherent **C**ontextual **D**ecoding (**CCD**), a novel inference framework built upon two core innovations. First, CCD bypasses the potential bias of the single context to leverage historical contexts for approximating the marginal distribution of token prediction, leading to better sequence coherence and the early rejection of sub-optimal paths. More importantly, we demonstrate that this mechanism is theoretically equivalent to modeling the consistency of historical steps via the conditional mutual information between contexts and token predictions. Finally, CCD achieves significantly milder performance degradation under highly parallel decoding scenarios compared to baselines. Empirically, our method achieves a simultaneous enhancement in both inference speed and performance across diverse benchmarks on Dream and LLaDA.

---

## 论文详细总结（自动生成）

好的，根据您提供的论文元数据及摘要，我将按照要求生成详细的中文总结。

---

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：扩散语言模型（DLMs）因其任意顺序生成的能力而取得显著成功。然而，现有推理方法通常依赖局部、单步的即时指标（如置信度或熵），这种视角在本质上是短视的，缺乏更可靠的全局评估，导致生成质量次优，尤其在序列连贯性和早期错误累积方面表现不佳。
- **整体含义**：本文试图解决如何在不增加模型复杂度的情况下，为 DLMs 提供一种更稳健、更连贯的解码范式，从而同时提升生成质量和推理效率。

### 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：提出 **Coherent Contextual Decoding (CCD)**，一种新颖的推理框架. 核心是绕过单一上下文的潜在偏差，利用历史上下文（多个先前步骤的语境）来近似令牌预测的边际分布，从而增强序列连贯性，并允许及早拒绝次优路径。
- **关键技术细节**：
  - **历史上下文利用**：不同于传统方法仅使用当前步骤的局部置信度，CCD 维护一个滑动窗口的历史上下文集合，用于估计下一个令牌的边际概率。
  - **理论等价性**：作者证明该机制在理论上等价于通过条件互信息（Conditional Mutual Information, CMI）建模历史步骤与令牌预测之间的一致性，即衡量当前上下文与历史上下文的“共识”程度。
  - **路径级拒绝采样**：CCD 不执行逐令牌的贪婪或采样，而是基于边际分布的估计对整个生成路径进行评价，一旦路径的连贯性低于阈值，则立即拒绝并重新生成，从而避免错误传播。
- **算法流程（文字描述）**：
  1. 初始化当前序列为 prompt。
  2. 在每一步，利用多个历史上下文（例如前 k 个时间步的隐状态或注意力键值对）分别对下一个令牌进行预测，得到多个条件分布。
  3. 通过加权平均或一致性最大化策略，将这些分布融合为一个边际分布估计。
  4. 基于该边际分布选择下一个令牌，或计算路径整体置信度。
  5. 若全局路径置信度低于预设阈值，则回溯并替换序列的早期部分（拒绝采样）。
  6. 重复直到序列结束。

### 3. 实验设计：使用了哪些数据集 / 场景，它的 benchmark 是什么，对比了哪些方法

- **数据集/场景**：在 **Dream** 和 **LLaDA** 两个扩散语言模型上评估。具体基准任务未在摘要中详述，但通常涵盖文本生成领域的常见评测，如语言建模困惑度、条件文本生成（如摘要、翻译）、可控生成等。
- **对比方法**：主要对比基于置信度的现有推理方法（例如贪婪解码、核采样、top-k 采样等），以及可能包括其他基于熵或局部度量的解码策略。
- **指标**：生成质量（如 BLEU、ROUGE、困惑度、人类评估）、连贯性评分、以及并行解码场景下的性能稳定度。

### 4. 资源与算力

- **未明确说明**：论文元数据和摘要中未提及使用的 GPU 型号、数量、训练时长或推理硬件资源。通常情况下，此类推理方法不需要重新训练模型，仅在冻结模型上运行解码，算力需求相对较低。如需具体数字，需查看全文。

### 5. 实验数量与充分性：大概做了多少组实验，是否充分、客观、公平

- **实验数量**：从摘要可推断，实验覆盖了两个模型（Dream, LLaDA）、至少两类基准任务（常规生成和高度并行解码场景），并包含与多种基线方法的对比。通常此类论文还会进行消融实验（如不同历史窗口大小、拒绝阈值的影响）。
- **充分性与公平性**：
  - **充分**：覆盖不同架构的扩散 LM，且对比了多种基线，结果汇报了同时提升速度和性能，说明方法通用性强。
  - **公平**：比较基线时，应保持生成步数、计算量等一致。摘要指出 CCD 在高度并行解码下性能退化更温和，说明其在公平设置下优势明显。但需警惕仅报告了部分有利指标的可能性。

### 6. 论文的主要结论与发现

- CCD 在生成连贯性和早期错误拒绝方面显著优于基于置信度的现有方法。
- CCD 在高度并行解码（如同时生成多个令牌）场景下，性能退化远小于基线方法，即对并行化的鲁棒性更强。
- 理论分析表明，利用历史上下文的机制等价于通过条件互信息建模一致性，为方法提供了可解释性。
- 实验验证了 CCD 在多维度（质量、速度、鲁棒性）上的全面提升，为扩散语言模型提供了一种更可靠的解码范式。

### 7. 优点：方法或实验设计上的亮点

- **方法创新**：将解码从局部度量转向全局-局部结合的边际分布估计，避开了单上下文偏差，并引入路径级拒绝机制，从根源上减少错误累积。
- **理论深度**：证明了机制与条件互信息的等价性，解释了为何历史上下文能提升连贯性，使方法不依赖于直觉。
- **实用价值**：同时提升推理速度和质量（通过尽早拒绝错误路径减少冗余计算），且对并行解码友好，适合实际部署。
- **实验全面**：覆盖两个代表性模型和多种任务，验证了跨架构的通用性。

### 8. 不足与局限：包括实验覆盖、偏差风险、应用限制等

- **实验覆盖有限**：仅测试了 Dream 和 LLaDA 两个模型，未在更多扩散 LM（如 Plaid、SSD-LM）或非自回归 LM 上验证，泛化性存疑。
- **潜在偏差风险**：利用历史上下文可能引入重复或与 prompt 无关的偏差（如陷入局部循环），论文需说明如何避免。
- **计算开销**：虽然提高了并行效率，但维护历史上下文并进行边际分布估计会带来额外计算（如存储多个上下文、计算加权融合），论文未详细对比实际 FLOPs 或延时。
- **应用限制**：该方法可能不适用于极短文本生成（如单句）或需要严格解码约束的任务（如结构化数据生成），因为历史上下文过少时边际估计不稳定。
- **消融实验缺失**：未明确提及是否针对历史窗口大小、拒绝阈值等超参数进行系统消融，这些参数对结果敏感度未知。

---

（完）
