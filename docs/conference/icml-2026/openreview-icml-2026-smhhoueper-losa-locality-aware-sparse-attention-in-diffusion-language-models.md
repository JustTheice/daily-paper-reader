---
title: "LoSA: Locality Aware Sparse Attention in Diffusion Language Models"
title_zh: LoSA：扩散语言模型中的局部感知稀疏注意力
authors: "Haocheng Xi, Harman Singh, Yuezhou Hu, Coleman Richard Charles Hooper, Rishabh Tiwari, Aditya Tomar, Minjae Lee, Wonjun Kang, Michael W. Mahoney, Chenfeng Xu, Kurt Keutzer, Amir Gholami"
date: 2026-04-30
pdf: "https://openreview.net/pdf/5ccb837ca6766956dc06d4704002b2371fa7dc80.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 扩散语言模型中的稀疏注意力
tldr: 针对扩散语言模型在长上下文场景下的推理效率瓶颈，发现块级扩散中token表征变化具有局部性（仅少数活跃token更新显著），据此提出LoSA稀疏注意力机制，利用该局部性减少KV缓存访问开销，有效解决了扩散模型中KV膨胀问题。实验表明，LoSA在保持生成质量的同时大幅提升推理速度。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散LM长上下文推理受限于内存密集型注意力，现有稀疏注意力的KV膨胀问题。
method: 提出LoSA，利用token更新局部性仅关注活跃token。
result: 显著加速长上下文推理而不损失质量。
conclusion: 利用扩散模型的动态局部性可以有效设计高效的稀疏注意力。
---

## Abstract
Block-wise diffusion language models (DLMs) generate multiple tokens in parallel, offering a promising alternative to autoregressive decoding. However, their inference efficiency remains bottlenecked by memory-bound attention in long-context scenarios. Naïve sparse attention is ineffective for DLMs due to the KV inflation problem: different queries select different prefix positions, causing the union of accessed KV pages to remain large.
To address this challenge, we observe that block-wise diffusion exhibits locality of representation changes across denoising steps: only a small fraction of tokens (active tokens) undergo significant hidden-state updates, while most tokens (stable tokens) remain nearly unchanged.
Based on this insight, we propose LoSA (Locality-aware Sparse Attention), which reuses cached prefix-attention results for stable tokens and applies sparse attention only to active tokens with large representation changes. This design reduces the number of queries contributing to the union of KV indices, substantially shrinking the KV pages that must be loaded.
Across multiple block-wise DLMs and reasoning benchmarks, LoSA preserves near-dense accuracy while significantly improving efficiency, achieving up to 4.14× speedup over dense attention on RTX A6000 GPUs. LoSA also delivers up to 5% average improvement over baselines across all datasets and configurations, demonstrating the effectiveness of the proposed method.

---

## 论文详细总结（自动生成）

# LoSA：扩散语言模型中的局部感知稀疏注意力 — 论文详细总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **研究动机**：扩散语言模型（DLM）能够并行生成多个 token，是自回归解码的有前景替代方案。然而在长上下文场景下，其推理效率受限于内存密集型的注意力计算。
- **背景问题**：朴素稀疏注意力对 DLM 无效，因为存在 **KV 膨胀问题**：不同查询选择不同的前缀位置，导致需要加载的 KV 页面（页）的并集仍然很大，无法有效减少内存访问。
- **核心观察**：论文发现块级扩散在去噪步骤中表现出 **表征变化的局部性**：只有一小部分 token（活跃 token）的隐藏状态发生显著更新，而大部分 token（稳定 token）几乎不变。
- **整体含义**：利用这一局部性可以设计高效的稀疏注意力，在保持生成质量的同时大幅提升推理速度。

## 2. 论文提出的方法论：核心思想、关键技术细节
- **核心思想**：提出 **LoSA（Locality-aware Sparse Attention）**，通过区分活跃 token 与稳定 token，减少注意力计算中需要加载的 KV 页面数量。
- **关键技术细节**：
  - 对 **稳定 token**：复用缓存的 prefix 注意力结果（即之前计算过的注意力权重或上下文向量），避免重新计算。
  - 对 **活跃 token**（隐藏状态变化大的 token）：应用稀疏注意力，仅关注这些活跃 token 与 KV 缓存中的相关部分。
  - 这种设计减少了参与 KV 索引并集的查询数量，从而大幅缩小必须加载的 KV 页面。
- **算法流程（文字描述）**：
  1. 在每一去噪步骤中，计算当前 token 表示相较于上一步骤的变化幅度，识别活跃 token。
  2. 对活跃 token，使用稀疏注意力（例如仅选择 top-k 的 KV 位置）。
  3. 对稳定 token，直接复用之前步骤的注意力输出，不重新查询 KV 缓存。
  4. 最终仅需加载活跃 token 对应的 KV 页面，避免了全量 KV 加载。

## 3. 实验设计：数据集、基准与对比方法
- **数据集/场景**：论文在 **多个块级扩散语言模型** 以及 **推理基准（reasoning benchmarks）** 上评估。具体数据集名称未在摘要中列出，但暗示了覆盖常见长上下文推理任务。
- **基准（benchmark）**：以密集注意力（dense attention）作为主要对比基线。
- **对比方法**：除了 dense attention 外，还对比了其他基线（baselines），LoSA 在所有数据集和配置上平均提升 **5%**。
- **硬件环境**：使用 RTX A6000 GPU 进行速度测试。

## 4. 资源与算力
- **GPU 型号**：RTX A6000
- **GPU 数量与训练时长**：论文摘要中未明确说明使用的 GPU 数量和训练时长，只提及推理加速测量。可能主要关注推理效率，而非训练过程。

## 5. 实验数量与充分性
- **实验数量**：从摘要可推断，实验覆盖了多个块级 DLM 和多个推理基准，并进行了多组配置对比（不同数据集、不同方法），但具体数量未量化。
- **充分性与客观性**：
  - 优势：给出了与 dense attention 的速度对比（最高 4.14× 加速），以及相对于其他基线的平均提升（5%），表明实验设计包含主要竞争方法。
  - 不足：未提供消融实验的细节（例如活跃 token 阈值的影响、不同稀疏度下的精度-效率权衡），也缺乏对稳定 token 复用策略的深入分析。实验覆盖的模型规模和任务类型未明确说明，可能存在选择性报道。

## 6. 论文的主要结论与发现
- **主要结论**：LoSA 在保持接近密集注意力的精度的同时，大幅提升推理效率，最高获得 **4.14× 速度提升**（相对于 dense attention）。
- **关键发现**：
  - 扩散语言模型中的 token 更新具有天然局部性，这是设计高效稀疏注意力的重要基础。
  - 通过利用此局部性，可以有效解决 KV 膨胀问题，使稀疏注意力在 DLM 中变得可行且高效。
  - 在所有数据集和配置上，LoSA 平均超出基线 5%，证明了方法的有效性。

## 7. 优点：方法或实验设计上的亮点
- **方法论亮点**：
  - 创新性地识别并利用了 DLM 特有的“表征变化局部性”，为稀疏注意力提供了新思路。
  - 对稳定 token 复用缓存结果，避免了重复计算，是一种低开销的近似加速策略。
- **实验设计亮点**：
  - 在真实硬件（RTX A6000）上测量实际加速比，结果可信。
  - 同时报告了精度保持情况（near-dense accuracy）和速度提升，兼顾了质量与效率。
  - 在不同模型和数据集上验证了泛化性，并提供了平均提升（5%），避免了单一场景过拟合。

## 8. 不足与局限
- **实验覆盖有限**：未公开具体数据集名称和模型规模，可能仅限于特定推理任务（如数学推理、常识推理等），对更广泛的长文本生成（如对话、摘要）的效果未知。
- **缺乏消融细节**：未分析活跃 token 判定阈值、不同稀疏比率、复用策略对精度的影响，无法评估方法对不同超参数的敏感性。
- **偏差风险**：仅与 dense attention 和少数基线对比，未与更先进的稀疏注意力方案（如 StreamingLLM、H2O 等）比较，可能高估了方法优势。
- **应用限制**：方法依赖去噪过程中的局部性，该性质是否普遍存在于所有扩散语言模型（不同架构、不同任务）尚未充分验证。对稳定 token 的复用可能导致累积误差，尤其当 token 状态缓慢变化时。
- **算力信息缺失**：未说明训练或推理所用的 GPU 数量和耗时，限制了复现和成本估计。

（完）
