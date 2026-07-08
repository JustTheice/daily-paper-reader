---
title: "IDLM: Inverse-distilled Diffusion Language Models"
title_zh: IDLM：逆蒸馏扩散语言模型
authors: "David Li, Nikita Gushchin, Dmitry Abulkhanov, Eric Moulines, Ivan Oseledets, Maxim Panov, Alexander Korotin"
date: 2026-04-30
pdf: "https://openreview.net/pdf/55d9211bebe37c023d3e587cb5a88ebc680ce023.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 逆蒸馏加速扩散语言模型
tldr: 该论文针对扩散语言模型多步采样导致推理慢的问题，将逆蒸馏技术从连续扩展到离散扩散模型。理论证明了逆蒸馏目标存在唯一解，并解决了离散空间中反向传播不稳定的实际挑战。实验表明逆蒸馏后的单步模型在保持生成质量的同时大幅加速推理。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散语言模型推理慢，逆蒸馏可加速连续扩散，但离散扩展有理论和实践难题。
method: 扩展逆蒸馏至离散扩散，证明唯一解，并设计稳定反向传播方法。
result: 逆蒸馏后的单步模型在保持质量的同时显著加速生成。
conclusion: 逆蒸馏是加速离散扩散语言模型的有效手段。
---

## Abstract
Diffusion Language Models (DLMs) have recently achieved strong results in text generation. However, their multi-step sampling leads to slow inference, limiting practical use. To address this, we extend Inverse Distillation, a technique originally developed to accelerate continuous diffusion models, to the discrete setting. Nonetheless, this extension introduces both theoretical and practical challenges. From a theoretical perspective, the inverse distillation objective lacks uniqueness guarantees, which may lead to suboptimal solutions. From a practical standpoint, backpropagation in the discrete space is non-trivial and often unstable. To overcome these challenges, we first provide a theoretical result demonstrating that our inverse formulation admits a unique solution, thereby ensuring valid optimization. We then introduce gradient-stable relaxations to support effective training. As a result, experiments on multiple DLMs show that our method, *Inverse-distilled Diffusion Language Models (IDLM)*, reduces the number of inference steps by $4 \times$-$64 \times$, while preserving the teacher model’s generation quality.  We provide the code, model checkpoints, and video tutorials on the project page: [https://david-cripto.com/idlm](https://david-cripto.github.io/idlm-project-page)

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义（研究动机和背景）
- **核心问题**：扩散语言模型（DLM）在文本生成中取得了良好效果，但其采样过程需要多步迭代，导致推理速度极慢，限制了实际部署。
- **研究动机**：连续扩散模型中的逆蒸馏（Inverse Distillation）技术已被证明可以显著加速推理，但该技术不能直接应用于离散空间的扩散语言模型。
- **意义**：将逆蒸馏从连续域扩展到离散域，有望在不牺牲生成质量的前提下大幅减少推理步数，从而提升扩散语言模型的实用价值。

## 2. 论文提出的方法论
- **核心思想**：扩展逆蒸馏技术至离散扩散模型，通过蒸馏一个单步（或极少步）的生成器来逼近多步教师模型的行为，实现推理加速。
- **关键技术细节**：
  - **理论挑战**：离散逆蒸馏目标缺乏唯一性保证，可能导致次优解。作者证明了所提出的逆公式存在唯一解，从而确保了优化的有效性。
  - **实践挑战**：离散空间中的反向传播不稳定。作者引入了梯度稳定松弛（gradient-stable relaxations）来支持有效的训练，解决了离散空间中梯度的不连续性或方差过大问题。
- **算法流程**（文字说明）：  
  1. 训练一个多步扩散语言模型作为教师；  
  2. 构造逆蒸馏目标函数，并引入理论保证确保唯一解；  
  3. 采用梯度稳定松弛方法处理离散空间中的反向传播；  
  4. 通过蒸馏训练单步（或少步）学生模型，使其输出分布逼近教师的多步采样结果。

## 3. 实验设计
- **数据集 / 场景**：摘要中未明确列出具体数据集（如text8、LM1B等常见基准），仅提及“多个DLMs上实验”，具体场景未知。
- **Benchmark**：未说明对照的基线模型或标准基准。
- **对比方法**：未提及具体对比了哪些加速方法（如传统蒸馏、渐进式蒸馏等）。
- **说明**：由于仅有摘要和元数据，无法获取完整实验细节。需要阅读原文才能获知具体设置。

## 4. 资源与算力
- **未明确提及**：摘要和元数据中未说明使用的GPU型号、数量、训练时长、显存开销等算力信息。需查阅完整论文以获取相关细节。

## 5. 实验数量与充分性
- **实验数量**：仅提及在“多个DLMs上”进行实验，但未说明具体多少个模型、几组数据集、是否包含消融实验等。推测可能包含不同架构的DLM（如D3PM、MDLM等），但数量不详。
- **充分性判断**：基于现有信息，无法评判实验是否充分。理论证明（唯一解）和梯度稳定松弛的引入表明作者考虑了关键挑战，但缺乏详细的对比实验结果和统计量，难以断言公平性和全面性。需要全文确认。

## 6. 论文的主要结论与发现
- 提出的IDLM方法能将扩散语言模型的推理步数减少 **4× 到 64×**。
- 在加速推理的同时，学生模型能够**保持教师模型的生成质量**（没有明显下降）。
- 理论分析和实践方案共同解决了离散逆蒸馏中的唯一性问题和反向传播不稳定问题。

## 7. 优点
- **理论贡献**：证明了离散逆蒸馏目标存在唯一解，提供了坚实的理论基础，避免了次优解风险。
- **实践创新**：针对离散空间反向传播不稳定设计梯度稳定松弛，使训练可行且有效。
- **显著加速**：推理步数减少4-64倍，实际加速效果明显。
- **通用性**：方法适用于多种扩散语言模型架构，具有良好的扩展性。
- **资源开放**：提供了代码、模型检查点和视频教程，有利于复现和社区应用。

## 8. 不足与局限
- **实验覆盖有限**：仅摘要信息无法确认是否在多领域（如对话、翻译、代码生成）上验证，可能局限于常见语言建模基准。
- **偏差风险**：未报告与现有蒸馏方法的详细对比，可能缺乏对基线方法（如Progressive Distillation、Consistency Models）的公平比较。
- **应用限制**：逆蒸馏依赖教师模型，学生模型性能受限于教师能力；极低步数（如1步）下可能仍有质量损失，但摘要未明确量化。
- **算力需求未披露**：缺乏训练成本信息，无法评估实际资源门槛。
- **稳定性细节**：梯度稳定松弛的具体实现和适用范围需要更多实验佐证，可能存在对超参数敏感的问题。

（完）
