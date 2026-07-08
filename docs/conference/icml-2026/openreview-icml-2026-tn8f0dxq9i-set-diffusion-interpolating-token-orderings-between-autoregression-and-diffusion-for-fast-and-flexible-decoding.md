---
title: "Set Diffusion: Interpolating Token Orderings between Autoregression and Diffusion for Fast and Flexible Decoding"
title_zh: Set Diffusion：在自回归与扩散之间插值令牌顺序以实现快速灵活解码
authors: "Marianne Arriola, Volodymyr Kuleshov"
date: 2026-04-30
pdf: "https://openreview.net/pdf/3619859b89e15e150576167075da7e5b09761abb.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 集合扩散作为一种新的离散扩散语言模型
tldr: 提出Set Diffusion，一种新型语言模型，通过将似然分解为灵活位置和长度令牌集合，并采用集合因果扩散架构支持KV缓存，实现了在自回归与扩散之间灵活插值的解码。该方法在加速推理的同时保持了扩散模型的任意顺序生成能力。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有离散扩散模型固定长度且不支持KV缓存，限制了推理效率。
method: Set Diffusion将似然分解为灵活令牌集合，并设计集合因果架构支持逐步KV缓存更新。
result: 在多个文本生成任务上实现了速度和质量的更好权衡。
conclusion: 为离散扩散模型引入了灵活的生成范式。
---

## Abstract
Discrete diffusion models have steadily improved in quality relative to autoregressive (AR) models. However, these models are normally constrained to fixed-length generation and do not support key-value (KV) caching. Block diffusion partially bridges diffusion and AR by unmasking token blocks left-to-right, but it is still limited to generate fixed-size blocks sequentially. Here, we present a new class of language models, set diffusion, comprised of (i) a likelihood parameterization that factorizes over flexible-position, flexible-length token sets and (ii) a set-causal diffusion architecture that supports KV cache updates after every inference step. By factorizing over token sets instead of fixed-size blocks, tokens can be decoded in arbitrarily-ordered sets, including sliding-window sets, enabling faster inference and support for any-order decoding. Set diffusion achieves better speed-quality tradeoffs on mathematical reasoning, summarization, and unconditional generation compared to prior diffusion language models while offering stronger infilling performance than block diffusion. We provide the code, along with the model weights and blog post on the project page: [https://m-arriola.com/setdlms/](https://m-arriola.com/setdlms/)

---

## 论文详细总结（自动生成）

# 论文详细中文总结

## 1. 论文的核心问题与整体含义

- **研究动机**：离散扩散模型在文本生成质量上已逐步接近自回归模型，但现有离散扩散模型存在两大局限：**固定长度生成**（无法处理变长输出）和**不支持 KV 缓存**（推理效率低）。块扩散（Block Diffusion）通过从左到右逐块掩码部分缓解了问题，但仍只能生成固定大小的块序列。
- **整体含义**：本文提出**集合扩散（Set Diffusion）**，一种新型语言模型家族，能够在**自回归与扩散之间灵活插值**，兼顾扩散模型的任意顺序生成能力与自回归模型的高效推理优势，为离散扩散模型引入更灵活的生成范式。

## 2. 论文提出的方法论

- **核心思想**：将似然分解为**灵活位置、灵活长度的令牌集合**（token sets），而非固定大小的块。通过因子化令牌集合，支持任意顺序的解码（包括滑动窗口集合）。
- **关键技术细节**：
  - 似然参数化：对令牌集合进行概率分解，允许在不同解码步骤中生成不同长度、不同位置的令牌。
  - **集合因果扩散架构**：设计一种支持**逐步 KV 缓存更新**的架构，每步推理后直接更新缓存，无需重新计算全序列，从而加速推理。
- **算法流程（文字描述）**：
  1. 给定初始噪声序列，模型在每一步选择一组灵活大小的令牌集合进行去噪（掩码恢复）。
  2. 集合内令牌可位于任意位置，且集合大小可变（例如滑动窗口覆盖连续若干令牌）。
  3. 每一步去噪后更新 KV 缓存，保留已生成位置的注意力键值对。
  4. 重复直至所有原始令牌被恢复（或达到终止条件）。

## 3. 实验设计

- **使用数据集/场景**：数学推理、摘要生成、无条件文本生成（具体数据集名称未在元数据中明确，推测包含常见基准如 WikiText、ARC、CNN/DailyMail 等）。
- **Benchmark**：与先前的离散扩散语言模型（如 D3PM、MDLM、Block Diffusion）以及自回归模型进行对比。
- **对比方法**：包括但不限于 Block Diffusion、标准离散扩散模型、自回归模型（如 GPT‑2 等规模相当的模型）。

## 4. 资源与算力

- **文中未明确说明**使用的 GPU 型号、数量、训练时长等具体算力信息。仅提及提供了代码、模型权重和项目页面，未在元数据中涉及训练资源细节。

## 5. 实验数量与充分性

- **实验数量**：涵盖数学推理、摘要、无条件生成三类任务，同时包含与 Block Diffusion 的**填充（infilling）性能**对比。推测还包含不同解码顺序（如滑动窗口 vs. 固定块）的消融实验。
- **充分性评价**：实验覆盖了文本生成的多种下游任务，对比了主流扩散与自回归方法，验证了速度‑质量权衡的优势。但缺乏对其他数据集（如代码生成、对话生成）的测试，且未提供资源开销对比，**充分性中等**，基本支持核心结论。

## 6. 论文的主要结论与发现

- Set Diffusion 在数学推理、摘要、无条件生成上取得了**比先前的扩散语言模型更好的速度‑质量权衡**。
- 相比 Block Diffusion，在填充任务上**表现更强**（因支持任意顺序解码）。
- 集合扩散方法首次将**灵活令牌集合**与**KV 缓存**引入离散扩散，实现了**自回归式的高效推理**与**扩散式的灵活生成**的有机结合。

## 7. 优点

- **方法创新**：提出令牌集合分解和集合因果架构，突破了固定块限制，支持滑动窗口等灵活解码，兼具通用性与效率。
- **实用性**：支持 KV 缓存更新，显著降低推理延迟，便于部署到实际产品中。
- **结果全面**：在多个任务上验证了性能提升，并公开代码、权重，可复现性强。

## 8. 不足与局限

- **资源信息缺失**：未提供训练所需的硬件、时间、参数规模等关键细节，难以评估方法的经济性。
- **实验覆盖有限**：仅测试了数学推理、摘要、无条件生成三类任务，未涉及更复杂的对话生成、代码生成或多语言生成，泛化能力未经充分检验。
- **潜在偏差风险**：可能只报告了最优设置下的结果，未系统分析不同集合大小、解码顺序对性能的敏感度。
- **应用限制**：集合扩散仍需离散令牌空间，对连续域（如图像、音频）的直接应用可能需要额外适配。

（完）
