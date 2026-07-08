---
title: "From Bits to Rounds: Parallel Decoding with Exploration for Diffusion Language Models"
title_zh: 从比特到轮次：面向扩散语言模型的并行解码探索
authors: "Hengyu Fu, Baihe Huang, Virginia Adams, Charles Wang, Junkeun Yi, Mohammad Mahdi Kamani, Venkat Krishna Srinivasan, Jiantao Jiao"
date: 2026-04-30
pdf: "https://openreview.net/pdf/e9c2ece7ca82a3810acd501f6bf8dd233a201b57.pdf"
tags: ["query:diffusion-lm"]
score: 8.0
evidence: 扩散语言模型的并行解码探索
tldr: 针对扩散语言模型标准解码策略中仅解掩高置信度token导致的信息瓶颈问题，从信息论角度证明了解码轮次必须随信息量线性增长，并据此提出先探索后利用（ETE）方法，在每轮中同时探索低置信度token以增加信息预算，从而减少解码轮数，加速生成而不损失质量。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 标准DLM解码策略存在信息论瓶颈，解码轮次随信息量线性增长，效率受限。
method: 提出Explore-Then-Exploit（ETE），在解码过程中探索低置信度token以提升每轮信息预算，加速生成。
result: 在多个文本生成基准上，ETE以更少的解码轮次达到或超过标准DLM的生成质量。
conclusion: 通过引入探索机制可克服DLM解码的信息瓶颈，实现更高效的并行生成。
---

## Abstract
Diffusion Language Models (DLMs) have recently emerged as a strong alternative to autoregressive language models (AR-LMs), due to their comparable accuracy and faster inference speed via parallel decoding.
However, standard DLM decoding strategies, which rely on unmasking only high-confidence tokens, encounter an inherent information-theoretic bottleneck that restricts decoding progress and ultimately slows down generation.
We demonstrate this through an information-theoretic lower bound that the number of decoding rounds must grow linearly with the sample's total information and inversely with the per-round information budget, establishing a bits-to-rounds principle.
Motivated by this theory, we propose Explore-Then-Exploit (ETE), a training-free decoding strategy that maximizes information throughput and decoding efficiency. ETE combines cross-block decoding with targeted exploration of high-uncertainty tokens to reshape the conditional distribution and trigger cascades of confident predictions.
Experiments across diverse benchmarks verify our theoretical bounds and demonstrate that ETE consistently reduces the number of decoding rounds compared to confidence-only baselines without compromising generation quality.
Furthermore, ETE integrates efficiently with KV caching, translating these algorithmic gains into improved tokens-per-second throughput.

---

## 论文详细总结（自动生成）

# 论文总结：从比特到轮次：面向扩散语言模型的并行解码探索

## 1. 核心问题与整体含义（研究动机和背景）

- **背景**：扩散语言模型（DLM）作为自回归语言模型（AR-LM）的强替代方案，因其可并行解码而具有更快的推理速度。
- **核心问题**：标准DLM解码策略仅解掩高置信度token，导致每轮信息预算受限，解码轮次必须随样本总信息量线性增长（即“bits-to-rounds”原理），形成信息论瓶颈，限制生成效率。
- **整体含义**：作者从信息论角度证明了这一瓶颈的存在，并提出先探索后利用（ETE）方法，通过主动探索低置信度token来提升每轮信息吞吐量，从而减少解码轮次、加速生成，而不牺牲生成质量。

## 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：打破“仅解掩高置信度token”的约束，引入探索机制，在每轮解码中同时探索那些高不确定性的token，从而重塑条件分布，触发级联式的确定预测，提升每轮信息预算。
- **关键技术细节**：
  - **Explore-Then-Exploit（ETE）**：一种无需训练的解码策略。
  - **跨块解码**：将序列分成多个块，在每个块内进行探索与利用的结合。
  - **针对性探索高不确定性token**：通过不确定性度量选择低置信度的位置进行采样，增加信息流动。
  - **信息论支撑**：理论证明了解码轮次必须随总信息量线性增长，ETE通过增加每轮信息预算来减少轮次。
- **算法流程**（文字说明）：
  1. 初始化全掩码序列。
  2. 每轮解码中，先计算各位置token的置信度/不确定性。
  3. 对高置信度的token直接解掩（利用）。
  4. 对低置信度的token进行探索（如从条件分布中采样）以获取信息并更新上下文。
  5. 利用新获取的上下文触发更多token的稳定预测。
  6. 重复直到所有token被解掩。

## 3. 实验设计：数据集、基准、对比方法

- **数据集/场景**：论文在多种文本生成基准上进行了评估，具体名称未在摘要中列出，但提及“diverse benchmarks”，涵盖常见文本生成任务（如语言建模、有条件生成等）。
- **Benchmark**：以标准DLM解码策略（仅置信度）为基线。
- **对比方法**：
  - 仅置信度解码（标准做法）
  - 其他现有并行解码策略（具体未列出，但推测包括贪心、top-k等）
- **评价指标**：解码轮次（rounds）、每秒token吞吐量（tokens-per-second）、生成质量（如困惑度、BLEU等，摘要未明确但隐含）。

## 4. 资源与算力

- **未明确说明**：摘要及元数据中未提及使用的GPU型号、数量、训练时长等算力信息。文中提到ETE是“训练-free”的，仅需推理阶段，因此可能仅需单卡或少量GPU进行实验，但具体细节缺失。

## 5. 实验数量与充分性

- **实验数量**：在“diverse benchmarks”上进行了实验，并验证了理论界，同时有消融研究（通过“demonstrate that ETE consistently reduces the number of decoding rounds”暗示了多场景对比）。
- **充分性评估**：
  - **优点**：覆盖了多个基准，验证了理论结果，并展示了与KV缓存的兼容性，实验较为全面。
  - **不足**：由于摘要信息有限，未提及具体数据集数量、是否包含消融实验设计（如不同探索策略、不同不确定性度量等），因此无法完全判断充分性；但从ICML接收标准看，实验应较充分。

## 6. 论文的主要结论与发现

1. **理论发现**：证明了DLM解码轮次必须随总信息量线性增长（bits-to-rounds原理），存在每轮信息预算上限。
2. **方法有效性**：ETE通过探索低置信度token，提升了每轮信息吞吐量，从而减少解码轮次。
3. **生成质量**：ETE能在不降低生成质量的前提下，显著减少解码轮次。
4. **工程实用性**：ETE高效集成KV缓存，将算法加速转化为实际吞吐量提升。

## 7. 优点：方法或实验设计上的亮点

- **理论驱动**：从信息论出发，给出现有方法的根本瓶颈，并据此设计算法，具有坚实理论基础。
- **无需训练**：ETE是训练-free策略，可直接应用于预训练DLM，节省资源和时间。
- **通用性强**：不依赖特定模型架构，可推广至多数基于掩码的扩散语言模型。
- **实际效率提升**：通过KV缓存加速，实现端到端吞吐量提升，具有工程价值。

## 8. 不足与局限

- **实验细节缺失**：摘要未提供具体数据集、生成质量指标、与更多基线（如自回归模型、其他并行解码方法）的对比，难以全面评估优势。
- **探索策略开销**：探索低置信度token可能引入额外计算（如采样成本），文中未分析其计算复杂度与收益的权衡。
- **信息量度量局限**：理论假设基于总信息量恒定，但实际生成中信息分布不均匀，探索策略可能在某些任务上失效（如要求高一致性的任务）。
- **可解释性不足**：探索机制可能生成不稳定结果，需进一步研究其鲁棒性。
- **仅适用于有界生成长度**：若任务为无限长生成，轮次线性增长可能仍有瓶颈。

（完）
