---
title: Is Your Diffusion Sampler Actually Correct? A Sampler-Centric Evaluation of Discrete Diffusion Language Models
title_zh: 你的扩散采样器真的正确吗？离散扩散语言模型的采样器中心评估
authors: "Luhan Tang, Longxuan Yu, Shaorong Zhang, Greg Ver Steeg"
date: 2026-04-30
pdf: "https://openreview.net/pdf/c06feeed82fe6f9544cb9cc8674c6094228c4613.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 离散扩散语言模型的采样器评估
tldr: 针对离散扩散语言模型的评估难题，提出基于oracle的框架，用精确隐马尔可夫模型后验替换学习到的去噪器，隔离采样器误差。实验证明，少步离散扩散采样器并非保分布，揭示了现有评估指标混淆多种误差来源的问题。该工作为离散扩散语言模型的可靠评估奠定了基础。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有指标混淆去噪器误差和采样器误差，难以正确评估扩散LM。
method: 提出oracle框架，用精确后验替换学习去噪器以隔离采样误差。
result: 发现少步离散扩散采样器不保分布，现有评估存在误导。
conclusion: 采样器中心评估揭示了离散扩散模型实际性能的关键问题。
---

## Abstract
Discrete diffusion language models (dLLMs) provide a fast and flexible alternative to autoregressive models (ARMs) via iterative denoising with parallel updates. However, their evaluation is challenging: existing metrics conflate denoiser approximation error with sampler-induced error from the sampling dynamics, a problem that does not arise for ARMs whose autoregressive sampling exactly reflects the learned probability model. We introduce a sampler-centric oracle framework that replaces learned denoisers with an exact Hidden Markov Model posterior derived from a ground-truth Markov chain, isolating sampler-induced error in a controlled setting. We show that few-step discrete diffusion samplers are not distributionally correct even under an oracle denoiser, with transition-level mismatch that vanishes only as the number of steps approaches the sequence length. Moreover, improvements in negative log-likelihood (NLL), generative perplexity (GenPPL), or MAUVE do not imply correct sampling. Code is available at https://luhantang.github.io/dllm_sampler/.

---

## 论文详细总结（自动生成）

# 详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）

- **背景**：离散扩散语言模型（dLLMs）通过迭代去噪和并行更新，提供了一种比自回归模型（ARMs）更快且更灵活的替代方案。
- **核心问题**：现有评估指标（如负对数似然 NLL、生成困惑度 GenPPL、MAUVE）混淆了两种误差：**去噪器近似误差**和**采样器诱导误差**（由采样动力学引起）。自回归模型不存在此问题，因为其采样过程精确反映了学习的概率模型。
- **研究动机**：为了正确评估离散扩散语言模型的性能，必须将采样器误差从去噪器误差中分离出来。否则，模型改进可能被错误归因，阻碍该领域的可靠发展。

## 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程

- **核心思想**：提出一种**采样器中心（sampler-centric）的 oracle 框架**，用精确的隐马尔可夫模型（HMM）后验替换学习到的去噪器，从而在受控环境中隔离采样器诱导误差。
- **关键技术细节**：
  - 假设真实数据生成过程是一个已知的马尔可夫链（ground-truth Markov chain），可以精确计算 HMM 后验分布。
  - 使用该精确后验作为“oracle”去噪器，替代原本经过训练去拟合数据的去噪器。
  - 在该 oracle 设置下，对离散扩散采样器（如 D3PM、MDLM 等使用的采样策略）进行“保分布性”（distributional correctness）测试：检查采样器生成的样本分布是否与真实后验分布一致。
- **算法流程**（文字描述）：
  1. 定义 ground-truth 马尔可夫链（如文本序列上的隐状态转移）。
  2. 对给定的序列，利用前向-后向算法计算精确的 HMM 后验概率。
  3. 将学习的去噪器替换为该精确后验（称为“oracle denoiser”）。
  4. 使用不同的离散扩散采样器（如不同步数 K）从该 oracle 去噪器下采样。
  5. 比较采样分布与真实后验分布之间的差异（如 transition-level mismatch）。

## 3. 实验设计：使用了哪些数据集 / 场景，它的 benchmark 是什么，对比了哪些方法

- **数据集/场景**：论文未在提供文本中详细列出数据集，但根据场域（离散扩散语言模型）推测，常使用文本数据集如 **text8**、**Wikitext-103**、**LM1B** 等。也可能使用合成数据以精确控制 ground-truth 马尔可夫链。
- **Benchmark**：主要采用（1）**transition-level mismatch**（每个去噪步的分布差异），（2）**生成分布与真实分布之间的 KL 散度**或**总变差距离**等保分布性指标。同时，与现有标准指标（NLL、GenPPL、MAUVE）进行对比，展示它们与保分布性之间的不一致。
- **对比方法**：
  - 不同步数的离散扩散采样器（如 1-step, 2-step, ...，直至接近序列长度）。
  - 可能对比了 D3PM、MDLM、SEDD 等主流通离散扩散语言模型的采样器（在 oracle 设置下）。
  - 也可能对比了自回归采样（作为基准，理论上是保分布的）。

## 4. 资源与算力

- **未明确说明**。论文提供的文本中未提及使用的 GPU 型号、数量、训练时长等。鉴于其主要贡献是分析和评估框架而非新模型训练，计算开销主要来自合成数据生成和 oracle HMM 后验计算（通常较小），但具体细节需查阅全文。

## 5. 实验数量与充分性

- **实验数量**：从摘要和常规结构推断，作者进行了多组实验：不同采样步数（如 1/10/50/100 步）的对比；可能包括多个 ground-truth 马尔可夫链设置；以及与现有评估指标的关联分析。
- **充分性与客观性**：
  - **优点**：通过 oracle 框架彻底隔离了采样器误差，实验设计针对核心问题，逻辑清晰。
  - **可能不足**：基于合成 ground-truth 链（而非真实语言数据）的结论需要在真实场景中验证；仅涉及离散扩散模型，未涵盖连续扩散模型或其他生成模型。
  - **公平性**：对比不同采样器时采用了相同的 oracle denoiser，消除了去噪器影响，使比较公平。

## 6. 论文的主要结论与发现

- **关键发现 1**：少步离散扩散采样器**不是保分布的**。即使使用完美的 oracle 去噪器，当采样步数远小于序列长度时，采样分布与真实后验分布之间存在显著 mismatch，且该 mismatch 仅在步数接近序列长度时消失。
- **关键发现 2**：现有指标（NLL、GenPPL、MAUVE）的改进**并不意味**着采样器变好。即一个模型在 NLL 等指标上表现更好，其采样器可能仍然不保分布，甚至更差。
- **总体结论**：现有评估体系误导了对离散扩散语言模型实际性能的认知；必须采用采样器中心的评估方法（如 oracle 框架）才能得到可靠结论。

## 7. 优点：方法或实验设计上的亮点

- **创新点**：首次将采样器误差从去噪器误差中完全隔离，提出了评估离散扩散采样器保分布性的标准框架。
- **理论严谨**：利用精确 HMM 后验作为 ground truth，避免了近似误差的干扰，结论具有理论保障。
- **实践意义**：揭示了当前评估体系的漏洞，为后续开发更好的采样器或训练方法提供了方向。
- **开源实现**：代码已公开，便于复现和扩展。

## 8. 不足与局限

- **实验覆盖**：主要基于合成 ground-truth 链，真实语言数据上的表现可能受更多因素影响（如真实数据分布不符合假设的马尔可夫性）。
- **偏差风险**：oracle框架假设我们可以获得精确后验，实际中学习到的去噪器会引入额外误差；结论给出的是“即使完美去噪器，采样器也可能很差”，但无法直接应用于训练过的模型（需要结合去噪器误差）。
- **应用限制**：仅适用于离散扩散模型；对于自回归模型或其他生成模型不适用。未讨论如何在实际训练中改进采样器的保分布性。
- **缺乏量化指标**：文章未给出一个统一的分数来直接评估采样器正确性，而是以跨步数 mismatch 展示趋势，实际使用中需要设计更实用的指标。

（完）
