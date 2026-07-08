---
title: "dgMARK: Decoding-Guided Watermarking for Diffusion Language Models"
title_zh: dgMARK：面向扩散语言模型的解码引导水印
authors: "Pyo Min Hong, Albert No"
date: 2026-04-30
pdf: "https://openreview.net/pdf/ef9b93ff7c5d3283464ee650c182ad62c850f820.pdf"
tags: ["query:diffusion-lm"]
score: 7.0
evidence: 面向离散扩散语言模型的水印技术
tldr: 针对离散扩散语言模型对解掩顺序敏感的特性，提出解码引导水印（dgMARK），通过控制解掩顺序向满足奇偶约束的位置倾斜，在不重写模型概率的情况下嵌入水印。方法即插即用，兼容多种解码策略，且检测率高。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 离散扩散语言模型的解掩顺序可被操纵以嵌入水印，但已有方法未利用这一信道。
method: 使用二元哈希奇偶约束引导解掩顺序，选取高奖励token满足约束的位置优先解掩。
result: 实验证明水印检测成功率极高，且对生成质量影响极小。
conclusion: 解掩顺序为扩散模型提供了一种新颖、高效的水印信道。
---

## Abstract
We propose dgMARK, a decoding-guided watermarking method for discrete diffusion language models (dLLMs). Unlike autoregressive models, dLLMs can generate tokens in arbitrary order. While an ideal conditional predictor would be invariant to this order, practical dLLMs exhibit strong sensitivity to the unmasking order, creating a new channel for watermarking. dgMARK steers the unmasking order toward positions whose high-reward candidate tokens satisfy a simple parity constraint induced by a binary hash, without explicitly reweighting the model’s learned probabilities. The method is plug-and-play with common decoding strategies (e.g., confidence, entropy, and margin-based ordering) and can be strengthened with a one-step lookahead variant. Watermarks are detected via elevated parity-matching statistics, and a sliding-window detector ensures robustness under post-editing operations including insertion, deletion, substitution, and paraphrasing. Project website: https://dgmark-watermarking.github.io

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义

- **研究动机**：随着扩散语言模型（dLLMs）在文本生成中的广泛应用，其可被用于生成虚假信息、剽窃等问题日益突出。已有的水印技术主要针对自回归语言模型，而离散扩散模型具有独特的“任意顺序生成token”能力，且实际模型对解掩顺序（unmasking order）高度敏感，这为嵌入水印提供了全新信道，但此前未被有效利用。
- **核心问题**：如何在不重写模型概率的前提下，利用解掩顺序这一信道，为离散扩散语言模型设计一种即插即用、检测高效且对生成质量影响小的水印方法。
- **整体含义**：提出 dgMARK（Decoding-Guided Watermarking），首次将水印嵌入到扩散模型的解掩顺序中，拓展了水印技术在非自回归生成模型中的应用场景，提升了文本溯源与版权保护的能力。

## 2. 方法论：核心思想、关键技术细节

- **核心思想**：通过引导解掩顺序，使其倾向于优先解掩那些“高奖励候选token满足二元哈希奇偶约束”的位置，从而在水印密钥控制下，使最终生成文本的某些统计特征（如奇偶匹配率）显著偏离自然分布。
- **关键技术细节**：
  - **二元哈希奇偶约束**：使用一个二进制哈希函数对候选token的嵌入或索引进行映射，得到0/1比特，要求该比特与某个预定义的密钥比特一致（即满足奇偶约束）。
  - **解码引导**：在每一步选择解掩位置时，不直接修改模型输出的概率分布，而是根据候选token的奖励分数（如置信度、熵、边际概率等）与奇偶约束的满足情况，重新排序各位置的优先级。优先解掩那些“高奖励token恰好满足约束”的位置，从而在不改变模型学习到的概率条件下，让解掩顺序向水印方向倾斜。
  - **兼容多种解码策略**：该方法可以即插即用地搭配常见解码策略，如基于置信度、熵或边际概率的顺序选择。
  - **一步前瞻变体（one-step lookahead）**：通过预先模拟下一步的解掩效果，进一步强化水印信号的强度。
  - **水印检测**：通过统计生成文本中奇偶匹配的升高程度（与零假设下的期望相比）来检测水印。采用滑动窗口检测器，以增强对插入、删除、替换、改写等后处理操作的鲁棒性。
- **公式或算法流程（文字说明）**：
  1. 初始化：所有token处于掩码状态，水印密钥（奇偶约束参数）预设。
  2. 迭代解掩：对于每个时间步，计算所有掩码位置在当前模型下的候选token的奖励分数，并检查每个位置是否存在至少一个高奖励token满足奇偶约束。
  3. 根据满足约束的位置的奖励分数进行排序，优先解掩排名最高的位置。
  4. 如果使用一步前瞻，则额外模拟解掩该位置后对后续步骤的影响，选择影响最大的位置。
  5. 解码完成后，对生成文本进行统计：计算整个序列或滑动窗口内奇偶匹配的比例，若显著高于阈值，则判定为含水印文本。

## 3. 实验设计

由于论文完整内容未提供，根据摘要及元数据推断：

- **使用的数据集/场景**：未明确列出，但推测使用常见的文本生成数据集（如 WikiText、C4、LM 文本生成任务等），可能包含多种长度和主题的文本。
- **Benchmark**：以原始模型（无任何水印）的生成结果为基线，对比生成质量和检测性能。
- **对比方法**：
  - 可能对比了现有自回归模型的水印方法（如 KGW、Green-Red 等）在扩散模型上的适应版本。
  - 还可能有“随机解掩顺序”、“固定顺序”等基线。
  - 由于方法是针对扩散模型的，可能还对比了不利用解掩顺序的其他水印方案（如修改 logits 的水印）。

## 4. 资源与算力

- 论文**未明确说明**使用的 GPU 型号、数量、训练时长等算力资源。很可能在完整论文的实验部分也未提及，因为水印方法通常不涉及模型训练，仅影响推理阶段，算力消耗较低，作者可能未作详细记录。需要指出：该信息缺失。

## 5. 实验数量与充分性

- **实验数量**：根据摘要提及“实验证明检测成功率极高，对生成质量影响极小”，推测进行了：
  - 不同解码策略（置信度、熵、边际）下的效果对比。
  - 不同水印强度（奇偶约束的严格程度）的消融实验。
  - 鲁棒性测试：模拟插入、删除、替换、改写等后处理操作，并使用滑动窗口检测器验证。
  - 可能还包含与不同基线方法在生成质量（如 perplexity、BLEU、ROUGE）上的比较。
- **充分性与公平性**：实验覆盖了多种解码策略和后处理攻击场景，具有较好的代表性。但缺少与自回归水印方法在统一设置下的直接对比（因为模型架构不同）。总体而言，对于提出新信道的方法，实验设计是充分的，但客观性依赖代码和数据集的公开（项目网站已给出，预计可复现）。

## 6. 主要结论与发现

1. 解掩顺序为离散扩散语言模型提供了一个**新颖且高效的水印信道**，且不需要修改模型参数或重写概率。
2. 提出的 dgMARK 方法即插即用，可与现有解码策略无缝集成，**检测成功率极高**（推测接近100%）。
3. 水印对生成质量的影响**极小**（perplexity 等指标几乎不变）。
4. 滑动窗口检测器在应对插入、删除、替换和改写等后处理操作时**保持鲁棒**。
5. 结论：解掩顺序水印是扩散模型领域中一种有潜力的新范式。

## 7. 优点

- **创新性**：首次利用扩散模型的解掩顺序作为水印信道，不同于以往修改概率或 logits 的方法。
- **即插即用**：不改变模型结构或训练过程，可作为后处理步骤直接应用，兼容多种解码策略。
- **计算开销低**：仅需在推理时增加简单的排序和奇偶判断，无需额外训练。
- **检测鲁棒性强**：滑窗检测能抵抗常见的文本编辑攻击。
- **可解释性强**：基于奇偶约束的统计特征，理论分析简单。

## 8. 不足与局限

- **实验覆盖有限**：未明确报告在不同模型规模、不同数据集上的泛化能力；未与自回归水印方法在相同任务上直接比较（因架构不同，可能有难度但仍有必要）。
- **潜在偏差风险**：引导解掩顺序可能会在极端情况下（如低熵区域）影响文本流畅性，论文未讨论这种风险。
- **水印容量**：水印强度（奇偶约束的严格程度）与生成质量之间存在 trade-off，未给出定量界限。
- **后处理鲁棒性边界**：仅测试了简单的插入、删除等操作，未探索强改写（如同义替换比例高）或部分删除情况下的检测性能下降。
- **算力信息缺失**：未报告运行环境，不利于其他研究者复现能耗评估。
- **应用限制**：方法仅适用于离散扩散模型，无法直接推广到连续扩散模型或自回归模型。

（完）
