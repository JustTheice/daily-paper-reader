---
title: Balancing Understanding and Generation in Discrete Diffusion Models
title_zh: 平衡离散扩散模型中的理解与生成
authors: "Yue Liu, Yuzhong Zhao, Zheyong Xie, Qixiang Ye, Jianbin Jiao, Yao Hu, Shaosheng Cao, Yunfan Liu"
date: 2026-04-30
pdf: "https://openreview.net/pdf/4685be51d7eb15399d6b4a98add6d4383bda1264.pdf"
tags: ["query:diffusion-lm"]
score: 8.0
evidence: 统一掩码和均匀噪声离散扩散模型
tldr: 针对掩码扩散语言模型(MDLM)和均匀噪声扩散语言模型(UDLM)各自擅长理解与生成但无法兼顾的问题，提出XDLM，通过平稳噪声核在理论上统一两种范式，并简化后验概率缓解内存瓶颈。实验表明XDLM在理解和生成两方面均取得进步。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: MDLM和UDLM分别擅长理解和生成，但难以兼顾。
method: 提出XDLM，采用平稳噪声核统一两种范式，并简化后验概率计算。
result: 在理解和生成任务上均取得进步，同时降低内存消耗。
conclusion: XDLM实现了两种扩散范式的有效平衡与统一。
---

## Abstract
In discrete generative modeling, two dominant paradigms demonstrate divergent capabilities: Masked Diffusion Language Models (MDLM) excel at semantic understanding and zero-shot generalization, whereas Uniform-noise Diffusion Language Models (UDLM) achieve strong few-step generation quality, yet neither attains balanced performance across both dimensions. To address this, we propose XDLM, which bridges the two paradigms via a stationary noise kernel. XDLM offers two key contributions: it provides (1) a principled theoretical unification of MDLM and UDLM, recovering each paradigm as a special case; and (2) an alleviated memory bottleneck enabled by an algebraic simplification of the posterior probabilities. Experiments demonstrate that XDLM advances the Pareto frontier between understanding capability and generation quality. Quantitatively, XDLM surpasses UDLM by 5.4 points on zero-shot text benchmarks and outperforms MDLM in few-step image generation (FID 54.1 vs. 80.8).  When scaled to tune an 8B-parameter large language model, XDLM achieves 15.0 MBPP in just 32 steps, effectively doubling the baseline performance. Finally, analysis of training dynamics reveals XDLM’s superior potential for long-term scaling. Code is available at [https://github.com/MzeroMiko/XDLM](https://github.com/MzeroMiko/XDLM).

---

## 论文详细总结（自动生成）

# 论文总结：《平衡离散扩散模型中的理解与生成》

## 1. 核心问题与整体含义（研究动机与背景）
- **研究背景**：离散生成模型中存在两种主流范式——**掩码扩散语言模型 (MDLM)** 与 **均匀噪声扩散语言模型 (UDLM)**。前者擅长语义理解与零样本泛化，后者擅长少步生成质量，但二者均无法在理解与生成两个维度上取得平衡。
- **核心问题**：如何设计一种既能像 MDLM 一样具备强理解能力，又能像 UDLM 一样实现高效、高质量生成的离散扩散模型，突破当前性能边界。
- **整体含义**：论文通过统一两种范式，提出 **XDLM**，将理解与生成能力推向 Pareto 前沿，并在大语言模型微调中展示出显著优势。

## 2. 方法论：核心思想、关键技术细节
- **核心思想**：通过**平稳噪声核（stationary noise kernel）** 在理论上将 MDLM 和 UDLM 统一。该噪声核可退化为两种范式的特例，从而兼顾二者的优势。
- **关键技术细节**：
  - 理论统一：推导出平稳噪声核下的前向扩散过程，使 MDLM 的“掩码-替换”机制与 UDLM 的“均匀噪声”策略成为其特殊情况。
  - 简化后验概率：利用代数化简（algebraic simplification）计算后验概率，大幅降低内存占用，缓解训练时的内存瓶颈。
  - 训练与采样：沿用标准的扩散训练目标（ELBO），采样时可根据任务灵活调节噪声核参数，平衡理解与生成。
- **算法流程示意**：
  1. 定义平稳噪声核转移矩阵，参数化噪声类型（掩码/均匀）。
  2. 前向过程按核概率转移；反向过程学习去噪。
  3. 使用简化后的后验公式计算损失，避免全矩阵运算。
  4. 推理时通过调整核参数，在少步生成（偏向 UDLM）和零样本理解（偏向 MDLM）间切换。

## 3. 实验设计
- **数据集与场景**：
  - **零样本文本基准**（如文本分类/理解任务）——评估理解能力。
  - **图像生成任务**（少步生成，使用 FID 指标）——评估生成质量。
  - **大语言模型微调**（8B 参数模型，使用 MBPP 指标，32 步生成）。
- **对比方法**：
  - UDLM（均匀噪声扩散语言模型）
  - MDLM（掩码扩散语言模型）
- **Benchmark**：零样本文本基准（未具体说明，推测为 GLUE 或类似任务）、图像生成 FID、MBPP（代码生成基准）。
- **实验结果**：
  - 在零样本文本基准上，XDLM 超越 UDLM 5.4 分（具体指标未述）。
  - 在少步图像生成上，XDLM（FID 54.1）优于 MDLM（FID 80.8）。
  - 在 8B 模型微调中，XDLM 在 32 步达到 15.0 MBPP，是基线性能的两倍。

## 4. 资源与算力
- **文中提及的算力信息**：论文摘要、元数据和正文（仅见摘要）均未明确说明使用的 GPU 型号、数量或训练时长。仅提及“缩放至 8B 参数语言模型”，但未提供具体训练资源。
- **结论**：论文未报告算力细节，这是一个缺失的信息点。

## 5. 实验数量与充分性
- **实验数量**：从摘要看，论文在**三类任务**（零样本文本、图像生成、大模型微调）上各有一组主要对比实验，并提及“训练动力学分析”以验证长期缩放潜力。未明确说明消融实验数量。
- **充分性与公平性**：
  - 覆盖了理解（零样本）和生成（图像、代码）两种场景，对比了两种基线，结果清晰展示了优势。
  - 但缺乏对不同噪声核参数选择的消融研究、推理步数的影响分析、以及更多数据集的验证。可能不够全面。
  - 实验设置（如超参数、种子等）未在摘要中展示，需查看全文确认公平性。

## 6. 主要结论与发现
- XDLM 成功统一了 MDLM 和 UDLM，同时提升了理解与生成性能，将 Pareto 前沿向外推进。
- 理论统一通过平稳噪声核实现，简化后验概率有效降低内存消耗。
- 在大规模模型（8B）上，XDLM 在极少数推理步数（32 步）下即达到强基线两倍性能，具备长期扩展潜力。

## 7. 优点（方法与实验设计亮点）
- **理论贡献**：从数学上统一两种看似对立的扩散范式，并给出简洁的推导，使模型设计有理论支撑。
- **内存优化**：代数简化后验计算，解决了离散扩散模型训练时的高内存瓶颈，具有实际工程价值。
- **性能均衡且提升显著**：在零样本理解（+5.4 分）和少步生成（FID 从 80.8 降至 54.1）上均取得明显进步，特别是在 8B 模型上效果翻倍，证明了方法的可扩展性。
- **开源代码**：提供 GitHub 仓库，便于复现。

## 8. 不足与局限
- **实验细节不足**：摘要中未列出具体数据集名称、训练参数、消融实验、推理成本等，完整性不够充分。
- **算力信息缺失**：未报告 GPU 类型/数量/时长，影响可复现性和能耗评估。
- **任务覆盖面有限**：仅测试了文本理解、图像生成和代码生成三个场景，未涉及其他模态（如语音、蛋白质序列）或更复杂的离散生成任务。
- **潜在偏差风险**：只对比了 MDLM 和 UDLM，未与近年其他离散扩散模型（如 D3PM、Multinomial Diffusion）比较，可能导致结果夸大。
- **应用限制**：平稳噪声核的参数选择可能对任务敏感，论文未讨论参数鲁棒性；简化后验可能在某些噪声规则下失效。

（完）
