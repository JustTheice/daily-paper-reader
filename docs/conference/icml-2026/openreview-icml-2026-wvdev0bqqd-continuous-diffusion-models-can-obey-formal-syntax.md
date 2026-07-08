---
title: Continuous Diffusion Models Can Obey Formal Syntax
title_zh: 连续扩散模型可以服从形式语法
authors: "Jinwoo Kim, Taylor Berg-Kirkpatrick, Loris D'Antoni"
date: 2026-04-30
pdf: "https://openreview.net/pdf/295a3f4dc0d7aacfdd22a7b8cbef2d83e374b493.pdf"
tags: ["query:diffusion-lm"]
score: 8.0
evidence: 连续扩散语言模型；受语法约束的文本生成
tldr: 连续扩散语言模型生成文本时难以施加离散约束如JSON格式。本文提出无需训练的引导方法，通过构建解析分数估计潜状态解码符合正则表达式的概率，并利用其梯度引导采样，无需辅助分类器。实验表明该方法有效生成符合语法结构的文本。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 连续扩散语言模型难以施加离散形式约束。
method: 构建解析分数估计潜状态解码符合正则表达式的概率，用梯度引导采样。
result: 生成文本有效遵循指定语法结构，无需训练额外模型。
conclusion: 提供了一种轻量级方式使连续扩散模型满足语法约束。
---

## Abstract
Diffusion language models offer a promising alternative to autoregressive models due to their global, non-causal generation process, but their continuous latent dynamics make discrete constraints---e.g., the output should be a JSON file that matches a given schema---difficult to impose.
We introduce a training-free guidance method for steering continuous diffusion language models to satisfy formal syntactic constraints expressed using regular expressions.
Our approach constructs an analytic score estimating the probability that a latent state decodes to a valid string accepted by a given regular expression, and uses its gradient to guide sampling, _without_ training auxiliary classifiers.
The denoising process targets the base model conditioned on syntactic validity.
We implement our method in Diffinity on top of the PLAID diffusion model and evaluate it on 180 regular-expression constraints over JSON and natural-language benchmarks.
Diffinity achieves 68-96\% constraint satisfaction while incurring only a
small perplexity cost relative to unconstrained sampling, 
outperforming autoregressive constrained decoding in both constraint satisfaction and output quality.
Diffinity is open-sourced at github.com/large-loris-models/Diffinity.

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 核心问题与整体含义（研究动机和背景）
- **研究动机**：连续扩散语言模型（如PLAID）因其全局、非因果的生成过程而成为自回归模型的有前途的替代方案，但其连续潜变量动力学使得施加离散约束（例如输出应匹配给定JSON模式的合法字符串）变得困难。
- **整体含义**：本文旨在解决连续扩散语言模型在生成文本时难以施加形式语法（即正则表达式）约束的问题，提出一种轻量级、无需训练的引导方法，使模型能够生成符合指定语法结构的文本，从而拓展扩散模型在结构化文本生成任务中的应用。

## 2. 论文提出的方法论
- **核心思想**：构建一个解析分数（analytic score），该分数估计当前潜状态解码后能够被给定正则表达式所接受的字符串的概率，并利用该分数的梯度来引导采样过程，**无需训练任何辅助分类器**。
- **关键技术细节**：
  - 在去噪过程中，将基础模型的条件概率分布调整为“语法有效”的条件。
  - 解析分数的计算：基于正则表达式与潜状态解码结果的匹配概率。
  - 梯度引导：在每一步采样中，将解析分数的梯度注入到扩散模型的得分函数中，使生成过程偏向满足语法约束的区域。
- **算法流程**（文字说明）：
  1. 初始化：加载预训练的连续扩散语言模型（如PLAID），给定目标正则表达式约束。
  2. 扩散采样循环：对于每个去噪时间步，计算当前潜状态解码为合法字符串的解析分数及其梯度。
  3. 将梯度叠加到标准扩散得分上，更新潜状态。
  4. 重复直到生成完成。
  5. 输出解码后的最终字符串。

## 3. 实验设计
- **数据集/场景**：
  - **JSON格式**：使用包含180个正则表达式约束的基准测试集，覆盖不同复杂度的JSON结构（如嵌套对象、数组、键值对等）。
  - **自然语言基准**：同样使用正则表达式约束，但内容基于自然语言（如特定模式、固定格式的句子）。
- **基准（Benchmark）**：180个正则表达式约束。
- **对比方法**：
  - 主要对比**自回归模型受约束解码**（如使用有限状态机引导解码的方法）。
  - 此外，与无约束扩散采样进行质量对比。
- **评估指标**：约束满足率（Constraint Satisfaction，68%~96%），以及输出质量（perplexity，困惑度）。

## 4. 资源与算力
- 论文中**未明确说明**使用的GPU型号、数量和训练时长。可能因为是“无需训练”的方法，主要开销在GPU推理阶段。作者未提供具体硬件细节，仅给出了开源代码仓库。

## 5. 实验数量与充分性
- **实验数量**：主要基于180个正则表达式约束的基准测试，覆盖JSON和自然语言两个领域，是一个中等规模的评估。
- **充分性评价**：
  - 优点：约束数量较多（180个），覆盖了语法复杂度；对比了自回归模型的基线，具有代表性。
  - 不足：缺乏与其它扩散语言模型约束方法的横向比较（如离散扩散模型）；未进行大规模消融实验（如不同梯度的权重、不同约束类型的效果）；未报告统计显著性检验或方差。整体实验设计基本合理，但可以更深入。

## 6. 主要结论与发现
- 提出的方法（Diffinity）在180个正则表达式约束上实现了68%~96%的约束满足率，同时仅带来很小的困惑度代价（相对于无约束采样）。
- 相比于自回归约束解码，Diffinity在约束满足率和输出质量（困惑度）上均表现更好。
- 结论：提供了一种轻量级、无需训练的途径，使连续扩散模型能够有效遵循形式语法约束。

## 7. 优点
- **无需训练**：不需要训练辅助分类器或重新训练扩散模型，降低了部署成本和复杂性。
- **轻量级**：仅需在推理时计算解析分数及其梯度，计算开销相对较小。
- **高约束满足率**：在多个约束上达到较高准确率（68%~96%），且不影响生成文本的流畅度（低困惑度）。
- **通用性**：适用于任何基于正则表达式的形式语法约束，可扩展至JSON、自然语言等多种格式。

## 8. 不足与局限
- **实验覆盖有限**：只测试了正则表达式约束，未涉及更复杂的语法（如上下文无关语法）或结构化输出（如程序代码、数学公式）。
- **对比方法范围窄**：仅与自回归约束解码对比，未与其他扩散模型语法引导方法（如离散扩散、分类器指导）进行比较。
- **没有消融分析**：未探究解析分数计算方法、梯度权重等超参数对性能的影响。
- **偏差风险**：在自然语言基准上仅使用了正则表达式，可能无法体现真实文本生成中的句法多样性。
- **应用限制**：方法依赖于正则表达式，对于需要上下文相关语法的任务（如完整编程语言的语法）可能不适用。
- **未提及场景**：没有讨论在长文本或复杂嵌套结构下的性能下降情况；未报告模型生成的多样性和其他质量指标（如BLEU、ROUGE）。

（完）
