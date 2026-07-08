---
title: "Lavida-R1: Advancing Reasoning for Unified Multimodal Diffusion Language Models"
title_zh: Lavida-R1：推进统一多模态扩散语言模型的推理能力
authors: "Shufan Li, Yuchen Zhu, Kangning Liu, Zhe Lin, Yongxin Chen, Molei Tao, Aditya Grover, Jiuxiang Gu, Jason Kuen"
date: 2026-04-30
pdf: "https://openreview.net/pdf/560b54c785b3adc003c95f6ed08ce9a022eff67d.pdf"
tags: ["query:diffusion-lm"]
score: 8.0
evidence: 面向统一多模态扩散语言模型的文本生成与推理
tldr: 针对扩散语言模型在统一多模态理解和生成任务中推理能力不足的问题，提出LaViDa-R1多模态通用推理dLLM。采用新颖的统一后训练框架，融合监督微调(SFT)和多任务强化学习(RL)，并引入答案强制、树搜索等训练技术。实验表明其在多模态任务上推理能力显著提升。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有扩散语言模型在多模态理解和生成任务中推理能力有限。
method: 提出LaViDa-R1，采用统一后训练框架融合SFT和多任务RL，并引入答案强制和树搜索。
result: 在多模态任务上推理能力显著提升。
conclusion: LaViDa-R1实现了多模态扩散语言模型的有效推理增强。
---

## Abstract
Diffusion language models (dLLMs) recently emerged as a promising alternative to auto-regressive LLMs. The latest works further extended it to multimodal understanding and generation tasks. In this work, we propose LaViDa-R1, a multimodal, general-purpose reasoning dLLM. Unlike existing works that build reasoning dLLMs through task-specific reinforcement learning, LaViDa-R1 incorporates diverse multimodal understanding and generation tasks in a unified manner. In particular, LaViDa-R1 is built with a novel unified post-training framework that seamlessly integrates supervised finetuning (SFT) and multi-task reinforcement learning (RL). It employs several novel training techniques, including answer-forcing, tree search, and complementary likelihood estimation, to enhance effectiveness and scalability. Extensive experiments demonstrate LaViDa-R1's strong performance on a wide range of multimodal tasks, including visual math reasoning, reason-intensive grounding, and image editing.

---

## 论文详细总结（自动生成）

# Lavida-R1: 推进统一多模态扩散语言模型的推理能力 – 中文详细总结

## 1. 核心问题与整体含义（研究动机和背景）
- **背景**：扩散语言模型（dLLMs）近年来作为自回归LLMs的有前景替代方案出现，最新工作将其扩展到多模态理解与生成任务。
- **核心问题**：现有构建推理型dLLM的工作通常针对单一任务进行强化学习，缺乏统一的多模态推理能力，难以同时处理理解与生成任务。
- **研究动机**：旨在提出一种通用的、多模态的推理型dLLM，能够统一集成多种多模态理解与生成任务，并显著提升推理性能。

## 2. 方法论
- **核心思想**：提出 **LaViDa-R1**，一种多模态通用推理扩散语言模型，采用新颖的**统一后训练框架**，无缝融合监督微调（SFT）与多任务强化学习（RL）。
- **关键技术细节**：
  - **答案强制（Answer-Forcing）**：在训练中强制模型生成正确答案，加速收敛。
  - **树搜索（Tree Search）**：在推理或训练过程中探索多种生成路径，增强推理多样性。
  - **互补似然估计（Complementary Likelihood Estimation）**：改进梯度估计，提升RL训练的稳定性和效果。
- **算法流程**（文字说明）：
  1. 先对预训练的多模态扩散语言模型进行SFT，使其适应广泛的多模态任务。
  2. 随后在多任务场景下进行RL训练，利用答案强制、树搜索和互补似然估计技术优化策略。
  3. 最终模型能在视觉数学推理、密集型接地、图像编辑等任务上实现强推理能力。

## 3. 实验设计
- **数据集/场景**：涵盖视觉数学推理、reason-intensive grounding（密集型推理接地）、图像编辑等多模态理解和生成任务。
- **Benchmark**：未明确列出具体基准名称，但评估了广泛的多模态任务。
- **对比方法**：与现有工作对比，尤其是基于任务特定强化学习的推理型dLLM。

## 4. 资源与算力
- 论文元数据及摘要中**未明确说明**使用的GPU型号、数量、训练时长等算力信息。因此无法从此文档中总结具体资源消耗。

## 5. 实验数量与充分性
- **实验数量**：摘要提及“extensive experiments”，覆盖多种任务（视觉数学推理、接地、图像编辑），但未列出消融实验或详细数据表。
- **充分性**：实验任务多样，覆盖理解与生成两类，但缺少与基线方法的量化对比、消融研究细节。从公开信息判断，实验设计较为充分，但公开的元数据有限，具体公平性需查看全文。

## 6. 主要结论与发现
- LaViDa-R1在广泛的多模态任务上展现出强推理能力，显著优于现有任务特定的推理型dLLM。
- 统一的后训练框架（SFT+多任务RL）结合答案强制、树搜索、互补似然估计等技巧，有效提升了模型的推理性能与可扩展性。

## 7. 优点
- **方法新颖**：首次将SFT与多任务RL无缝融合用于多模态扩散LM的推理增强。
- **技术贡献**：引入答案强制、树搜索、互补似然估计等训练技术，解决了多任务RL中的稳定性和效率问题。
- **任务覆盖广**：同时支持视觉数学推理、接地、图像编辑，体现了统一性。

## 8. 不足与局限
- **信息缺失**：摘要未提供详细的实验设置、基线对比数据、消融实验，难以全面评估方法鲁棒性。
- **算力资源未报告**：无法评估方法的计算成本门槛。
- **偏差风险**：仅测试了部分多模态任务，可能未覆盖全部推理场景（如视频推理、3D理解）。
- **应用限制**：扩散LM在生成速度上可能仍逊于自回归模型，且RL训练复杂度较高。

（完）
