---
title: "Efficient-DLM: From Autoregressive to Diffusion Language Models, and Beyond in Speed"
title_zh: 高效扩散语言模型：从自回归到扩散语言模型及超越速度
authors: "Yonggan Fu, Lexington Whalen, Zhifan Ye, Xin Dong, Shizhe Diao, Jingyu Liu, Chengyue Wu, Hao Zhang, Enze Xie, Song Han, Maksim Khadkevich, Jan Kautz, Yingyan Celine Lin, Pavlo Molchanov"
date: 2026-04-30
pdf: "https://openreview.net/pdf/0b38d4d8e72b2fc349662e733066d6f9a2bf88c3.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 从自回归到扩散语言模型的转换以加速文本生成
tldr: 该论文针对扩散语言模型学习效率低于自回归模型的问题，提出从自回归到扩散语言模型的转换方法。通过分析现有转换方法的注意力和目标限制，提出保持预训练自回归权重分布的关键策略。实验表明转换后的扩散模型在速度上大幅提升，同时保持任务准确率。该工作为利用预训练自回归模型构建高效扩散语言模型提供了可扩展方案。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散语言模型并行生成效率高，但从头训练学习效率低于自回归模型，需探索转换方法。
method: 系统比较不同注意力模式，发现保持预训练自回归权重分布是关键，并据此提出可扩展转换方法。
result: 转换后的扩散模型在保持任务准确率的同时显著提升生成速度。
conclusion: 所提转换方法有效利用预训练自回归模型，为构建高效扩散语言模型提供可行路径。
---

## Abstract
Diffusion language models (dLMs) have emerged as a promising paradigm enabling parallel generation, but their learning efficiency lags behind that of autoregressive (AR) language models when trained from scratch. To this end, we study AR-to-dLM conversion, which transforms pretrained AR models into efficient dLMs that excel in speed while preserving AR models’ task accuracy. We achieve this by identifying limitations in the attention patterns and objectives of existing AR-to-dLM methods and then proposing methodologies and actionable insights for scalable AR-to-dLM conversion. Specifically, we first systematically compare different attention patterns and find that maintaining pretrained AR weight distributions is key to effective AR-to-dLM conversion. Accordingly, we introduce a continuous pretraining scheme with a block-wise attention pattern. We find that, in addition to block-wise attention’s known benefit of enabling KV caching, its block-wise causality better preserves pretrained AR models’ weight distributions, leading to a win–win in accuracy and efficiency. Second, to mitigate the training–test gap in mask token distributions (uniform vs. highly left-to-right), we propose a position-dependent token masking strategy that assigns higher masking probabilities to later tokens during training to better mimic test-time behavior. These studies lead to the Efficient-DLM model family, which outperforms state-of-the-art AR models and dLMs in accuracy–throughput trade-offs; for example, our Efficient-DLM-8B achieves +5.4\%/+2.7\% higher accuracy with 4.7$\times$/2.8$\times$ higher throughput compared to Dream-7B and Qwen3-4B, respectively.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）
- **研究动机**：扩散语言模型（dLM）具备并行生成的优势，但从头训练时学习效率低于自回归（AR）语言模型。如何利用已有的预训练 AR 模型高效地转换为 dLM，使其在保持任务准确率的同时大幅提升生成速度，是一个重要挑战。
- **整体含义**：论文提出从自回归到扩散语言模型的转换方法（AR-to-dLM conversion），通过分析现有转换方法在注意力模式（attention patterns）和目标（objectives）上的局限，提出保持预训练 AR 权重分布的关键策略，最终实现高效、可扩展的转换方案。

## 2. 方法论
- **核心思想**：维持预训练自回归模型的权重分布是有效转换的关键，并据此设计新的注意力模式与掩码策略。
- **关键技术细节**：
  - **注意力模式对比**：系统比较不同注意力模式（如全注意力、分块注意力等），发现分块注意力（block-wise attention）因其分块因果性（block-wise causality）能更好地保留预训练 AR 模型的权重分布，同时支持 KV 缓存，实现准确率与效率的双赢。
  - **连续预训练方案**：提出采用分块注意力模式的连续预训练（continuous pretraining），在保留预训练权重的同时训练扩散模型的去噪过程。
  - **位置依赖的掩码策略（position-dependent token masking）**：针对训练-测试时掩码分布不一致（训练时均匀掩码，测试时高度从左到右）的问题，提出在训练时对靠后的 token 赋予更高掩码概率，以更好地模拟推理时的行为。
- **公式/算法说明**：论文没有给出具体公式，但通过文字描述了上述方法。整体流程为：从预训练 AR 模型出发，采用分块注意力和位置依赖掩码进行连续预训练，得到高效的扩散语言模型家族 Efficient-DLM。

## 3. 实验设计
- **数据集与场景**：摘要未明确指定具体数据集，但提及对比了“state-of-the-art AR models and dLMs”，推断使用了标准自然语言理解和生成 benchmark（如可能包括 GLUE、SuperGLUE、文本生成任务等）。
- **Benchmark**：以 Dream-7B 和 Qwen3-4B 作为主要对比基准，评估准确率与吞吐量。
- **对比方法**：
  - 最先进的自回归模型（如 Qwen3-4B）
  - 最先进的扩散语言模型（如 Dream-7B）
- **核心对比结果**：Efficient-DLM-8B 相比 Dream-7B 准确率提升 5.4%，吞吐量提升 4.7 倍；相比 Qwen3-4B 准确率提升 2.7%，吞吐量提升 2.8 倍。

## 4. 资源与算力
- 论文摘要及元数据未提及具体使用的 GPU 型号、数量或训练时长。仅说明模型为 8B 规模，但未详述预训练/转换的计算开销。需要指出文中未明确说明。

## 5. 实验数量与充分性
- **实验组数**：摘要仅给出了主要对比结果（两个对比方法），未列举消融实验或其他数据集的结果。但根据论文标题和 ICML-2026 接收论文的常见做法，通常会有多组实验，包括注意力模式消融、掩码策略消融、不同模型规模对比等。
- **充分性与公平性**：摘要表明方法在准确率-吞吐量权衡上显著优于现有方法，实验设计相对客观。但由于缺乏完整实验细节（如多个数据集、多种模型尺寸、不同训练设置），无法全面评估其稳定性与泛化能力。

## 6. 主要结论与发现
- 自回归到扩散语言模型的转换是可行的，且通过保持预训练权重分布（分块注意力）和预测练-测试分布对齐（位置依赖掩码）可以显著提升扩散模型的效率。
- 所提出的 Efficient-DLM 模型家族在保持任务准确率的同时，实现了数倍的吞吐量提升，证明了方法的有效性。

## 7. 优点
- **方法创新**：系统分析了注意力模式对转换的影响，提出了“保留预训练权重分布”这一关键洞察，并据此设计具体方案。
- **实用性**：直接利用已有的高精度预训练 AR 模型，无需从零训练扩散模型，降低训练成本，易于推广。
- **性能突出**：在 8B 规模下同时提升准确率和吞吐量，展示了良好的 trade-off 曲线。
- **研究价值**：为扩散语言模型的构建提供了新范式，弥合了 AR 模型与扩散模型之间的差距。

## 8. 不足与局限
- **实验覆盖有限**：摘要仅报告了与两个具体模型的对比，缺乏更多标准化基准（如 LAMBADA、HellaSwag 等）以及不同模型尺寸（如 1B、2B 等）的全面实验。
- **细节缺失**：未说明使用的具体数据集、训练超参数、预训练来源等，限制了复现性。
- **算力信息缺失**：没有给出训练或转换的计算成本，难以评估方法的实际资源需求。
- **潜在偏差**：仅与特定版本（Dream-7B、Qwen3-4B）对比，可能未涵盖最新最强的模型；且只报告了准确率和吞吐量，未涉及生成质量的其他维度（如困惑度、多样性、可控性）。
- **应用限制**：仅针对语言模型，尚未验证是否适用于多模态或代码等其他领域；分块注意力的最优块大小可能依赖具体任务。

（完）
