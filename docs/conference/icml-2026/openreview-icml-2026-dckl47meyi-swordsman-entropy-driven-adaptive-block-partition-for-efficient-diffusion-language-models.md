---
title: "Swordsman: Entropy-Driven Adaptive Block Partition for Efficient Diffusion Language Models"
title_zh: Swordsman：面向高效扩散语言模型的熵驱动自适应块划分
authors: "Yu Zhang, Xinchen Li, Jialei Zhou, Hongnan Ma, Zhongwei Wan, Yiwei Shi, Duoqian Miao, Qi Zhang, Longbing Cao"
date: 2026-04-30
pdf: "https://openreview.net/pdf/6e04f1d674178fd74bcd5b251b9961b6e7d699ed.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 扩散语言模型的自适应块划分
tldr: 现有块级扩散语言模型的块划分固定且刚性，破坏语义/句法成分。受熵减假设启发，提出Swordsman框架，利用熵分析自动识别成分边界进行自适应块划分。该方法在多个语言生成任务上提升了速度和质量，实现了更符合语言结构的并行解码。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 固定块划分破坏语义完整性，影响生成质量。
method: 基于熵分析自适应识别成分边界，动态划分解码块。
result: 在速度和生成质量上均优于固定块基线。
conclusion: 熵驱动的自适应块划分能更好地匹配语言结构，提升扩散模型性能。
---

## Abstract
Block-wise decoding effectively improves the inference speed and quality in diffusion language models (DLMs) by combining inter-block sequential denoising and intra-block parallel unmasking. However, existing block-wise decoding methods typically partition blocks in a rigid and fixed manner, which inevitably fragments complete semantic or syntactic constituents, leading to suboptimal performance. Inspired by the entropy reduction hypothesis (ERH), we recognize that constituent boundaries offer greater opportunities for uncertainty reduction, which motivates us to employ entropy analysis for identifying constituent boundaries. Therefore, we propose Swordsman, an entropy-driven adaptive block-wise decoding framework for DLMs. Swordsman adaptively partitions blocks by identifying entropy shifts between adjacent tokens to better align with semantic or syntactic constituent boundaries. In addition, Swordsman dynamically adjusts unmasking thresholds conditioned on the real-time unmasking status within a block, further improving both efficiency and stability. As a training-free framework, supported by KV Cache, Swordsman demonstrates state-of-the-art performance across extensive evaluations. Our code is now available.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：扩散语言模型（DLM）中的块级解码虽然通过“块间顺序去噪 + 块内并行解掩”提升了推理速度和质量，但现有方法采用**固定、刚性**的块划分方式，往往会切断完整的语义或句法成分，导致生成质量出现次优。
- **研究动机**：受**熵减假设（ERH）**启发，作者认为成分边界处具有更大的不确定性降低潜力，因此可以利用**熵分析**来识别语言成分边界，从而实现更符合语言结构的块划分。
- **整体含义**：提出一种**训练无关**的自适应块划分框架 Swordsman，通过动态识别相邻 token 的熵变化来划分块，并在解码过程中依据块内实时解掩状态动态调整解掩阈值，在提升效率的同时保持生成质量。

### 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：利用**熵驱动**的方式自适应划分块，使每个块尽量对齐语义或句法成分边界，从而保留语言结构的完整性。
- **关键技术细节**：
  - **熵分析**：基于 ERH，计算相邻 token 的熵变化，当熵变化较大时视为成分边界，据此划分块。
  - **自适应块划分**：根据实时熵分布动态决定块的大小和边界，而非固定长度。
  - **动态阈值调整**：在块内解码过程中，根据当前已解掩 token 的“解掩状态”实时调整未解掩 token 的阈值，以兼顾效率与稳定性。
  - **KV Cache 支持**：作为训练无关框架，直接集成 KV Cache 加速推理。
- **公式或算法流程**（文字描述）：
  1. 输入序列，计算每个 token 位置的熵值（或熵变化）。
  2. 在熵值显著上升的点处切割，形成初始块边界。
  3. 对每个块内 token 执行并行解掩，同时监测块内已解掩 token 的置信度。
  4. 根据实时解掩状态动态调整剩余 token 的阈值，若块内多数 token 已确定，则提前停止迭代或调整采样温度。
  5. 利用 KV Cache 缓存中间状态，加速连续块的顺序去噪过程。

### 3. 实验设计：使用了哪些数据集 / 场景，benchmark 是什么，对比了哪些方法

- **数据集/场景**：摘要中未明确列出具体数据集（如 WikiText、LM1B、XSum 等），仅提到“multiple language generation tasks”以及“extensive evaluations”。
- **Benchmark**：未提及具体基准名称，仅声称在广泛评估中达到**最新（SOTA）**水平。
- **对比方法**：摘要中仅提及与“fixed block baselines”对比，即固定块划分的 DLM 方法。未列出具体对比模型名称（如 Mask-Predict、D3PM、BERT-like 扩散模型等）。

### 4. 资源与算力

- **未明确说明**：摘要及元数据中没有提及使用的 GPU 型号、数量、训练时长或推理加速比等具体算力信息。只能推测由于方法是训练无关的，可能只需要推理阶段的算力消耗，但缺乏具体数据。

### 5. 实验数量与充分性

- **实验数量**：摘要未列出具体实验组数（如不同数据集、消融实验、超参敏感性分析等）。元数据中“result”提到“速度和质量均优于固定块基线”，但无量化指标。
- **充分性与公平性**：由于缺少实验细节（如多个随机种子、数据集规模、显著性检验等），难以判断实验是否充分客观。仅从摘要看，对比基线单一（仅固定块），且无与近期其他自适应方法的对比，**实验覆盖明显不足**。但作为学术论文（ICML 2026 接收），预计全文应有更充分实验。

### 6. 论文的主要结论与发现

- **主要结论**：熵驱动的自适应块划分能更好地匹配语言结构（语义/句法成分边界），相比固定块划分在**推理速度**和**生成质量**上均有提升。
- **关键发现**：
  - 成分边界处的熵变化可作为块划分的有效信号。
  - 动态调整解掩阈值可以进一步提高效率与稳定性。
  - Swordsman 作为一种训练无关框架，易于集成到现有 DLM 中。

### 7. 优点：方法或实验设计上的亮点

- **方法论亮点**：
  - **训练无关**：无需额外训练或微调，直接适用于已预训练的 DLM，降低部署成本。
  - **自适应性强**：动态块划分和阈值调整能够适应不同序列的语言结构，保留语义完整性。
  - **理论基础扎实**：借用熵减假设（ERH）解释成分边界的合理性，有认知语言学支撑。
  - **高效**：通过并行解掩和 KV Cache 提升速度。
- **实验设计亮点**（若存在）：如果全文包含消融实验（如不同熵阈值的影响、不同块划分策略对比），则可视亮点，但摘要未体现。

### 8. 不足与局限

- **实验覆盖不足**：摘要中未列出具体数据集、指标、对比方法及消融实验，细节缺失严重，难以评估方法在不同任务上的泛化能力。
- **偏差风险**：只对比了固定块基线，未与其他自适应方法（如基于语言模型 perplexity 的分块、基于规则的分句）对比，可能存在选择性偏差。
- **应用限制**：
  - 熵计算的准确性依赖于 DLM 本身对 token-level 不确定性的建模能力，若模型熵估计不准，块划分可能失效。
  - 动态阈值调整可能引入额外超参数（如调整幅度、频率），需在全文说明是否鲁棒。
  - 目前只适用于 DLM 序列生成，对非自回归或连续扩散模型是否有效未知。
- **算力与效率量化缺位**：未报告推理加速倍数、内存占用等量化指标，无法验证实际收益。
- **数据集未开放**：虽然代码已公开，但实验数据集若未公开可能影响复现。

（完）
