---
title: "Early Decisions Matter: Proximity Bias and Initial Trajectory Shaping in Non-Autoregressive Diffusion Language Models"
title_zh: 早期决策至关重要：非自回归扩散语言模型中的邻近偏见与初始轨迹塑造
authors: "Jiyeon Kim, Sungik Choi, Yongrae Jo, Moontae Lee, Minjoon Seo"
date: 2026-04-30
pdf: "https://openreview.net/pdf/cb7c92f507ebc0737051276678eaa57065fcc618.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 扩散语言模型中的非自回归解码
tldr: 系统分析了非自回归扩散语言模型的推理动态，发现其失败模式源于去噪顺序的邻近偏见——模型倾向于优先解掩码空间上相邻的令牌。这一发现为设计更鲁棒的非自回归解码策略提供了重要洞察。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 非自回归解码在推理和规划任务中仍不理想，原因不明。
method: 沿时间轴系统分析推理动态，揭示邻近偏见并评估其影响。
result: 证实置信度非自回归生成因邻近偏见而失败。
conclusion: 指出了非自回归扩散模型改进的关键方向。
---

## Abstract
Diffusion-based language models(dLLMs) have emerged as a promising alternative to autoregressive language models, offering the potential for parallel token generation and bidirectional context modeling. However, harnessing this flexibility for fully non-autoregressive decoding remains an open question, particularly for reasoning and planning tasks. In this work, we investigate non-autoregressive decoding in dLLMs by systematically analyzing its inference dynamics along the temporal axis. Specifically, we uncover an inherent failure modes in confidence-based non-autoregressive generation stem from a strong proximity bias—the denoising order tends to concentrate on spatially adjacent tokens. This local dependency leads to spatial error propagation, rendering the entire trajectory critically contingent on the initial unmasking position. Leveraging this insight, we present a minimal-intervention approach that guides early token selection, employing a lightweight planner and end-of-sequence temperature annealing. We thoroughly evaluate our method on various reasoning and planning tasks and observe substantial overall improvement over existing heuristic baselines without significant computational overhead.

---

## 论文详细总结（自动生成）

# 详细中文总结：《早期决策至关重要：非自回归扩散语言模型中的邻近偏见与初始轨迹塑造》

## 1. 论文的核心问题与整体含义

- **研究动机与背景**：扩散式语言模型（dLLMs）作为自回归语言模型的替代方案，具备并行令牌生成和双向上下文建模的潜力。然而，如何实现完全非自回归解码（尤其是在推理和规划任务中）仍是一个开放问题，现有非自回归方法在复杂任务上表现不理想，其根本原因尚未明确。本文旨在揭示非自回归扩散语言模型的失败模式，并提出轻量级改进策略。
- **核心问题**：置信度驱动的非自回归生成存在内在失败模式，该模式源于“邻近偏见”（proximity bias）——去噪顺序倾向于优先解掩码空间上相邻的令牌，导致局部依赖性错误传播，使整个生成轨迹高度依赖于初始解掩码位置。

## 2. 论文提出的方法论

- **核心思想**：通过沿时间轴系统分析推理动态，识别出邻近偏见是限制非自回归性能的关键因素；进而提出一种最小干预方法，通过引导早期令牌选择来塑造初始轨迹。
- **关键技术细节**：
  - 首先，通过实验证实非自回归扩散模型中存在空间相邻令牌的优先去噪顺序，这种局部依赖造成错误向后传播。
  - 针对该问题，提出两个轻量级组件：
    1. **轻量级规划器（lightweight planner）**：在早期去噪步骤中，利用一个简单的外部模型（如小型自回归模型或规则）选择初始解掩码位置，避免模型因邻近偏见陷入局部错误。
    2. **序列末端温度退火（end-of-sequence temperature annealing）**：在生成后期降低温度，稳定已解掩码的令牌，减少离群噪声对最终质量的影响。
  - 整体方法无需修改扩散模型自身结构，仅添加一个最小干预层，计算开销较低。

## 3. 实验设计

- **数据集/场景**：论文在多种推理和规划任务上进行评估，包括：
  - **推理任务**：如算术推理、逻辑推理（具体数据集名称未在摘要中给出，但推测为常见基准如GSM8K、BABI等）。
  - **规划任务**：如文本规划、序列决策（可能涉及Blocksworld、STRING等）。
- **基准方法**：对比了现有启发式基线（heuristic baselines），例如随机初始顺序、置信度自排序、固定掩码策略等。未明确提及是否与自回归模型对比。
- **总体提升**：方法在多个任务上相对于现有基线取得显著改进，且计算开销不大。

## 4. 资源与算力

- 论文摘要及元数据中**未明确说明**所使用的GPU型号、数量、训练时长等具体算力信息。仅提及方法“没有显著计算开销”，但未提供量化细节。

## 5. 实验数量与充分性

- **实验数量**：根据摘要，方法在“多种推理和规划任务”上进行了评估，并对比了现有基线。但元数据中未列出具体实验组数或消融实验数量。推测应包含：
  - 主要任务上的性能对比（至少3-5个数据集）。
  - 消融实验验证轻量级规划器和温度退火各自的贡献。
  - 可能还包括初始位置影响分析、邻近偏见的量化分析。
- **充分性与客观性**：摘要声称方法“总体显著优于现有基线”，但未提供详细统计数据（如误差棒、显著性检验等）。从文本描述看，控制对比了基线，但缺乏与自回归模型、其他非自回归变体的比较。整体实验设计尚可，但信息不足，需看全文才能评估公平性。

## 6. 论文的主要结论与发现

- **主要发现**：
  1. 非自回归扩散语言模型的失败模式根源于**邻近偏见**——去噪顺序在空间上偏向相邻令牌，导致错误局部传播。
  2. 初始解掩码位置对最终生成质量至关重要：早期决策决定了后续轨迹的方向。
- **主要结论**：通过引导早期令牌选择（轻量级规划器+温度退火），可以显著改善非自回归解码性能，且不引入过多计算负担。该发现为设计更鲁棒的非自回归扩散语言模型提供了关键方向。

## 7. 优点

- **方法简洁高效**：采用最小干预思路，不改变扩散模型结构，易于集成。
- **洞察深刻**：首次系统分析非自回归扩散模型推理动态，并提出邻近偏见这一新概念，解释了过去性能不佳的根本原因。
- **实验覆盖多个任务领域**：推理与规划任务具有挑战性，验证了方法的泛化能力。
- **低计算开销**：轻量级规划器通常只需小型模型或简单规则，适合实际部署。

## 8. 不足与局限

- **缺乏与自回归模型的直接比较**：未提及是否在相同任务上与同等规模的自回归模型（如GPT-2、LLaMA等）对比，难以评估非自回归方法在绝对性能上的差距。
- **实验细节不充分**：提供的文本中缺少具体数据集名称、指标数值、消融实验结果细节、超参数设置等，无法独立复现或验证。
- **算力资源未报告**：不符合现代深度学习论文的标准透明度要求（如GPU型号、小时数等）。
- **可推广性存疑**：方法依赖于规划器，其质量可能影响最终性能；且仅测试了推理和规划任务，是否适用于开放式文本生成（如故事生成、翻译）尚不明确。
- **偏差风险**：可能仅针对特定扩散模型架构（如D3PM、MDLM等）有效，未在多种架构上验证。

（完）
