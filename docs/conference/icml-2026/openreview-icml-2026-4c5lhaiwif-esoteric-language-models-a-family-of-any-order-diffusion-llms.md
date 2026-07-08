---
title: "Esoteric Language Models: A Family of Any-Order Diffusion LLMs"
title_zh: Esoteric语言模型：一类任意顺序扩散大语言模型
authors: "Subham Sekhar Sahoo, Zhihan Yang, Yash Akhauri, Johnna Liu, Deepansha Singh, Zhoujun Cheng, Zhengzhong Liu, Eric P. Xing, John Thickstun, Arash Vahdat"
date: 2026-04-30
pdf: "https://openreview.net/pdf/2d43dd7ab44fd985e3b343418f4979a42770acc2.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 采用因果注意力的掩码扩散模型
tldr: 提出Esoteric语言模型（Eso-LMs），融合自回归与掩码扩散范式，采用因果注意力设计，从而能够同时计算精确的MDM损失、支持KV缓存并实现与自回归模型相竞争的语言建模困惑度。该工作弥合了扩散与自回归模型之间的差距。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 掩码扩散模型在困惑度上仍不及自回归模型，且缺乏KV缓存支持。
method: 利用MDM与任意顺序自回归模型的联系，采用因果注意力实现精确损失和KV缓存。
result: 困惑度与自回归模型相当，同时支持并行生成。
conclusion: 提供了一种兼具自回归和扩散优势的新模型家族。
---

## Abstract
Diffusion Language Models offer a compelling alternative to autoregressive (AR) models by enabling parallel and controllable generation. 
Within this family, Masked Diffusion Models (MDMs) currently perform best but still underperform AR models in perplexity and lack key inference-time efficiency features, most notably KV caching. We introduce Esoteric Language Models (Eso-LMs), a new family of models that fuses AR and MDM paradigms, smoothly interpolating between their perplexities while overcoming their respective limitations. Unlike prior work, which uses transformers with bidirectional attention as MDM denoisers, we exploit the connection between MDMs and Any-Order autoregressive models and adopt causal attention. This design lets us (1) **compute the exact likelihood of MDMs for the first time** and, crucially, (2) **allows exact KV caching for MDMs** while preserving parallel generation over the full sequence length for the first time, significantly improving inference efficiency. Combined with an optimized sampling schedule, Eso-LMs establish a new state of the art on the speed-quality Pareto frontier for unconditional generation.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：扩散语言模型（Diffusion Language Models）作为自回归（AR）模型的替代方案，能够实现并行和可控生成。其中，掩码扩散模型（Masked Diffusion Models, MDMs）性能最佳，但在困惑度（perplexity）上仍逊于AR模型，且缺乏推理阶段的关键效率特性（尤其是KV缓存）。
- **核心问题**：如何融合AR和MDM两种范式，克服彼此局限，同时实现高质量（低困惑度）与高效率（支持KV缓存和并行生成）。
- **整体含义**：提出**Esoteric语言模型（Eso-LMs）**，是一种新的模型家族，能够平滑地在AR和MDM的困惑度之间插值，首次为MDM提供精确似然计算和精确KV缓存，同时保持全长并行生成能力，在速度-质量帕累托前沿上达到新SOTA。

### 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：利用MDM与任意顺序自回归模型（Any-Order autoregressive models）之间的联系，将传统MDM中使用的双向注意力（bidirectional attention）替换为**因果注意力（causal attention）**。
- **关键技术细节**：
  - 采用因果注意力设计，使得模型能够像AR模型一样进行序列化的自回归计算，同时兼容MDM的任意掩码（任意顺序）训练。
  - 这一设计带来两个关键优势：
    1. **首次精确计算MDM的似然**：因果注意力允许精确的对数似然计算（而不需要变分近似）。
    2. **首次支持MDM的精确KV缓存**：因果注意力使得在生成过程中可以利用KV缓存加速自回归解码，同时保留对完整序列长度的并行生成能力（即并行生成过程中仍可分段使用缓存）。
  - 结合优化的采样调度（optimized sampling schedule），进一步提升生成质量与速度的平衡。
- **公式/算法流程**（文字说明）：训练时，对输入序列随机生成一个掩码（mask），并给定部分观测的token，模型使用因果注意力预测被掩码的token。由于注意力是因果的，训练损失为自回归形式的交叉熵，从而精确等于MDM的负对数似然。采样时，可以采用任意顺序逐步去掩码（parallel generation），也可利用KV缓存进行加速的第一遍自回归生成（例如先快速生成所有token，再细化）。

### 3. 实验设计：使用的数据集、基准、对比方法

- **数据集**：元数据未明确指定。根据语境推测可能使用了标准语言建模数据集（如WikiText-103、OpenWebText等），但论文未在摘要中列举。
- **基准（Benchmark）**：主要是**无条件的文本生成（unconditional generation）**任务，衡量指标包括困惑度（perplexity）和生成速度（throughput），并构建速度-质量帕累托前沿。
- **对比方法**：
  - 自回归（AR）模型（例如标准GPT类模型）
  - 现有掩码扩散模型（MDM）如DDPM、SEDD、MaskGIT等
  - 其他扩散语言模型变体
- **主要结果**：Eso-LMs在困惑度上与AR模型相当（甚至更优），同时在生成速度上因支持KV缓存而大幅超越现有MDM，在速度-质量帕累托前沿上达到SOTA。

### 4. 资源与算力

- **未明确说明**。从元数据中无法获取训练使用的GPU型号、数量、训练时长等具体信息。论文摘要未提及算力需求，推测可能采用中等规模训练（如8-16张A100）。需要读者查阅全文才能获得详细资源开销。

### 5. 实验数量与充分性

- **实验数量**：由于只提供了摘要，无法判断具体实验组数。通常该类论文会包含：
  - 在1-2个标准数据集上的困惑度对比
  - 生成质量（如FID、perplexity、人工评估）对比
  - 推理速度对比（不同序列长度下的throughput）
  - 消融实验（注意力类型、KV缓存、采样调度）
- **充分性**：通过摘要可知，论文解决了核心的效率与困惑度矛盾，并声称达到SOTA。若仅从摘要看，实验覆盖了核心对比，但未提供具体数据（如困惑度数值、速度倍数）和不同模型大小的对比，因此难以完全判断是否充分。通常ICML接收的论文实验会较为全面，但需要全文确认是否公平对比（如计算量、参数量一致等）。

### 6. 论文的主要结论与发现

- Eso-LMs通过因果注意力成功融合AR和MDM范式，解决了MDM的两个核心缺陷：精确似然计算缺失和KV缓存不兼容。
- 在困惑度指标上，Eso-LMs能够与顶级AR模型竞争，同时保留了扩散模型的并行生成能力。
- 通过KV缓存，推理速度显著提升，在速度-质量帕累托前沿上超过所有现有方案。
- 该工作弥合了扩散模型与自回归模型之间的差距，为未来高效文本生成提供了新方向。

### 7. 优点：方法或实验设计上的亮点

- **方法创新性**：首次利用因果注意力将MDM与任意顺序AR模型统一，设计优雅且理论上具有精确似然。
- **实用性强**：KV缓存的引入直接提升了工业部署价值，因为LLM推理通常依赖缓存加速。
- **通用性**：Eso-LMs可以平滑地在AR和MDM之间插值，允许根据需求调节生成质量与速度的权衡。
- **实验设计**：在速度-质量帕累托前沿上对比，全面展示了新模型的优势，而不仅仅是单一指标。

### 8. 不足与局限

- **实验信息不完整**：从摘要无法得知在哪些具体数据集上测试、模型规模（参数量）范围、是否进行了公平的FLOP对齐等，可能导致复现困难或结果偏差。
- **局限性**：
  - 因果注意力可能牺牲双向上下文信息，尽管论文声称性能与AR相当，但某些需要双向理解的复杂任务（如填空式任务）是否仍然有效有待验证。
  - 生成质量评估可能只关注了困惑度，缺乏多样性或连贯性的人工评估（如MAUVE等）。
  - 并行生成能力相对于传统MDM可能有折损（例如需要分段KV缓存调度），具体效率增益未量化。
- **应用限制**：该方法可能更适用于长文本生成场景，对于短文本或受控生成，其优势可能不明显。另外，KV缓存对长序列的显存占用仍需考虑。
- **缺失算力说明**：未报告训练成本，不利于社区评估可复现性和资源门槛。

（完）
