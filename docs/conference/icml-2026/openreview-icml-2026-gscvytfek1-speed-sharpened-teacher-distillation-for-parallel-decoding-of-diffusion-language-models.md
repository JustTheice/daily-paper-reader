---
title: "SPEED: Sharpened-Teacher Distillation for Parallel Decoding of Diffusion Language Models"
title_zh: SPEED：扩散语言模型并行解码的锐化教师蒸馏
authors: "Qiuhong Shen, Xingyi Yang, Xinyin Ma, Gongfan Fang, Xinchao Wang"
date: 2026-04-30
pdf: "https://openreview.net/pdf/7c1428777d6a30daa5c8a96f5f5e528f90c36be1.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 扩散语言模型；并行解码；文本生成
tldr: 扩散语言模型并行解码虽可加速但常降低质量。本文发现根本原因是过度分组使依赖令牌一起解码，违反条件独立性。提出SPEED框架，通过互补训练和推理设计扩大安全并行组，在多个文本生成任务上取得加速同时保持质量。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散语言模型并行解码加速但质量下降，需要理解原因并提出改进。
method: 分析令牌分组依赖性，提出SPEED框架，包括锐化教师蒸馏和自适应分组策略。
result: 在文本生成任务上，SPEED实现更高并行度且质量不降。
conclusion: 通过合理分组，扩散语言模型可安全加速并行解码。
---

## Abstract
Diffusion-based large language models generate text by gradually filling in masked tokens, yet they remain slow because they usually decode only a few tokens per step. Parallel decoding, which unmasks multiple tokens simultaneously, promises acceleration but often degrades quality when too many tokens are predicted at once. We identify the root cause: when decoding is viewed as iterative token grouping, overly permissive grouping places interdependent tokens in the same step, violates the conditional independence assumption, and amplifies reliance on noisy context even when the top prediction is already correct. We introduce SPEED, a framework that enlarges safe parallel groups through complementary training and inference designs. At training time, a sharpened teacher distillation objective selectively aligns the student to teacher-correct positions using a temperature-scaled KL term together with a masked language modeling loss, producing a student that assigns more probability mass to correct token identities and elevates more positions above the decoding threshold. At inference time, Slow-Fast Decoding partitions tokens by sensitivity to revealed context using token-wise Jensen-Shannon Divergence computed with and without access to the preceding block, decoding low-sensitivity tokens jointly in parallel while deferring high-sensitivity tokens until sufficient context resolves them. Through extensive experiments, our framework attains up to 12.2$\times$ speedup on LLaDA-8B-Instruct and 6.7$\times$ on Dream-7B-Instruct with accuracy close to greedy decoding across standard reasoning and code benchmarks.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）

- **研究动机**：基于扩散的大语言模型（Diffusion-based LLMs）通过逐步填充被掩码的令牌生成文本，但解码速度慢（通常每步仅解码少量令牌）。并行解码（一次性解除多个令牌的掩码）虽能加速，但若一次性预测过多令牌，往往导致生成质量下降。
- **根本原因识别**：论文将解码视为迭代式令牌分组，发现“过度分组”会将相互依赖的令牌放在同一解码步骤中，违反条件独立性假设，并放大对噪声上下文的依赖——即使顶部预测已经正确，也会引入质量损失。
- **整体目标**：在保证生成质量接近贪婪解码的前提下，实现显著的并行加速。

## 2. 方法论：核心思想、关键技术细节

- **核心思想**：通过互补的训练和推理设计，扩大“安全并行组”（即可以同时解码而不损害质量的令牌集合）。
- **训练阶段——锐化教师蒸馏（Sharpened Teacher Distillation）**：
  - 学生模型（student）通过一个温度缩放的KL散度项（temperature-scaled KL term）与教师模型（teacher）对齐，选择性聚焦于教师校正正确的令牌位置。
  - 同时结合掩码语言建模损失（masked language modeling loss），使得学生模型将更多概率质量分配给正确的令牌身份，并将更多位置提升至解码阈值以上（即更可靠地预测）。
- **推理阶段——慢速-快速解码（Slow-Fast Decoding）**：
  - 利用令牌级Jensen-Shannon散度（JSD）计算每个令牌对已揭示上下文的敏感性：分别计算在有/无前一个解码块信息时该令牌的预测分布差异。
  - 低敏感性令牌可被并行解码（快速），高敏感性令牌则推迟至获得足够上下文后再解码（慢速），从而在保证质量的同时提高并行度。

## 3. 实验设计

- **使用的数据集/场景**：标准推理（reasoning）和代码（code）基准测试。具体数据集名称未在摘要中列出，但提及了“standard reasoning and code benchmarks”。
- **Benchmark与对比方法**：论文未列出具体对比基线，但通过速度提升和精度接近贪婪解码来体现优势。对比对象应为贪婪解码（greedy decoding）以及可能的其他并行解码变体。
- **实验规模**：未说明消融实验的具体组数，但提到“extensive experiments”，且在不同模型上测试了加速效果。

## 4. 资源与算力

- 论文摘要中**未明确说明**使用的GPU型号、数量、训练时长等算力信息。仅提及在两个模型上进行了推理加速测试（LLaDA-8B-Instruct 和 Dream-7B-Instruct），但未提训练所需的计算资源。因此无法提供具体算力细节。

## 5. 实验数量与充分性

- **实验数量**：摘要中仅报告了在两种模型上的加速倍数（最高12.2×和6.7×）及精度接近贪婪解码。未列出多组数据集的具体结果、消融实验数量等。
- **充分性评估**：从摘要看，实验覆盖了不同规模的模型（8B和7B）及多类基准任务（推理+代码），但缺少对各个组件的消融（如单独考察训练蒸馏或推理分组的效果）。完整论文可能包含更详细的对比和消融，但摘要信息有限，无法完全判断其充分性。总体感觉实验设计是合理的，但公开信息不够全面。

## 6. 主要结论与发现

- **核心发现**：并行解码质量下降的根本原因是过度分组导致违反条件独立性假设。
- **SPEED框架有效性**：通过训练时锐化教师蒸馏 + 推理时慢速-快速分区解码，能够显著扩大安全并行组，实现高达12.2×（LLaDA-8B-Instruct）和6.7×（Dream-7B-Instruct）的加速，且精度接近贪婪解码。
- **结论**：扩散语言模型通过合理的令牌分组策略可以安全地实现并行解码加速，而不会牺牲生成质量。

## 7. 优点（方法或实验亮点）

- **问题根源分析透彻**：从条件独立性假设出发，明确指出过度分组是质量下降的根本原因，理论贡献清晰。
- **训练与推理协同设计**：训练阶段的蒸馏目标与推理阶段的分组策略互补，共同扩大并行组，而非仅改进其中一个环节。
- **自适应令牌分组**：推理阶段使用JSD计算令牌敏感性，自动区分可并行与需延迟的令牌，无需人工设定分组规则。
- **性能显著**：在8B和7B模型上取得数倍的加速，且质量无明显下降，实用性较强。

## 8. 不足与局限

- **实验细节披露不足**：现有摘要未列出具体数据集、对比基线、消融实验、超参数设置等，无法完全复现评估。
- **训练资源未知**：未提及训练阶段的计算开销，而训练一个锐化蒸馏模型可能也需要大量GPU资源。
- **泛化性存疑**：仅在两个扩散语言模型（LLaDA-8B-Instruct和Dream-7B-Instruct）上验证，是否适用于其他架构的扩散LM（如Genie、MDLM等）尚未说明。
- **可能的应用限制**：慢速-快速解码需要计算令牌级JSD，引入额外推理开销，在极端实时场景下可能抵消部分加速收益。摘要中未对比纯推理时间（包括预处理开销）。
- **理论分析有限**：虽然识别了根本原因，但未提供严格的理论保证或上界分析，主要依赖经验验证。

（完）
