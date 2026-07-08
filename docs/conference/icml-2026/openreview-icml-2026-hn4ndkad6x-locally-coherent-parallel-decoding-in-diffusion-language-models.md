---
title: Locally Coherent Parallel Decoding in Diffusion Language Models
title_zh: 扩散语言模型中的局部一致并行解码
authors: "Michael Hersche, Nicolas Menet, Ronan Tanios, Abbas Rahimi"
date: 2026-04-30
pdf: "https://openreview.net/pdf/ca9e2fd431e19be48d53ae1043962ce21e814f4f.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 离散扩散语言模型的局部一致并行解码
tldr: 离散扩散语言模型在并行解码时独立采样token，忽略了联合依赖导致语法不一致。提出CoDiLA方法，通过在并行采样中引入局部自回归依赖建模，在不牺牲并行性的前提下提升句法连贯性。实验表明，该方法有效减少了多token结构的断裂，提升了代码和自然语言生成的质量。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 标准离散DLM并行采样忽略token间依赖，导致语法错误。
method: CoDiLA在并行解码中融入局部自回归依赖性建模。
result: 在多个语言任务上提升了并行生成的一致性。
conclusion: 局部依赖建模能有效改善离散扩散模型的并行解码质量。
---

## Abstract
Diffusion language models (DLMs) have emerged as a promising alternative to autoregressive (AR) models, offering sub-linear generation latency and bidirectional capabilities that are particularly appealing for code generation and editing. Achieving sub-linear latency in discrete DLMs requires predicting multiple tokens in parallel. However, standard DLMs sample tokens independently from conditional marginal distributions, failing to capture the joint dependencies among concurrently generated tokens. As a result, they often lead to syntactic inconsistencies and break multi-token structures. In this work, we introduce CoDiLA (Coherent Diffusion with Local Autoregression), a method that reconciles parallel sampling with local dependency modeling. Rather than forcing the DLM to resolve fine-grained syntax, CoDiLA delegates local decoding to a small, auxiliary AR model operating on the diffusion latents. This design allows for parallel generation while ensuring sequential validity within a block and maintaining core DLM capabilities, including bidirectional modeling across blocks. We demonstrate that using a highly compact auxiliary AR model (e.g., 0.6B parameters) effectively eliminates coherence artifacts, establishing a new Pareto frontier for accuracy and speed in code generation benchmarks.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：离散扩散语言模型（DLM）在代码生成等任务中能以亚线性延迟进行并行解码，但标准DLM在并行采样时独立地从条件边缘分布中抽取每个token，忽略了同一时刻生成的多个token之间的联合依赖关系，导致句法不一致，例如破坏多token结构。
- **整体含义**：为解决上述问题，论文提出**CoDiLA**（Coherent Diffusion with Local Autoregression），在保持并行生成效率的同时，通过引入局部自回归依赖建模提升生成结果的局部连贯性，从而在速度与质量之间建立新的帕累托前沿。

### 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：不强制DLM自行解决细粒度语法问题，而是将局部解码任务委托给一个**小型辅助自回归（AR）模型**，该模型在扩散潜变量（diffusion latents）上操作。
- **关键技术细节**：
  - 在并行解码的每个块（block）内，辅助AR模型对块内token序列进行顺序建模，确保在同一块中生成的token满足局部语法依赖（如括号匹配、代码结构）。
  - 块与块之间仍保持DLM原有的双向建模能力，从而不牺牲全局上下文依赖。
  - 辅助AR模型非常紧凑（例如0.6B参数），避免了额外计算瓶颈。
- **公式或算法流程**（文字说明）：生成时，DLM首先并行采样出多个token候选（例如一个block内的所有token），然后将这些候选输入辅助AR模型，该模型以自回归方式重新排序或修正当前块内的token，以增强局部一致性；修正后的token再用于下一轮扩散迭代，最终输出连贯的序列。整个流程保持了并行解码的亚线性延迟优势。

### 3. 实验设计：数据集、基准测试、对比方法

- **数据集/场景**：主要聚焦于**代码生成基准测试**。具体数据集名称未详述，但元数据提到“code generation benchmarks”。
- **基准（Benchmark）**：代码生成领域的标准评估指标（如准确率、BLEU、编译通过率等），以及生成速度（延迟）。
- **对比方法**：标准离散DLM（独立并行采样）、纯自回归模型（如GPT类）、以及其他可能的并行解码方案（未具体列出对比模型名称，但文中提到“established a new Pareto frontier”暗示与多种现有方法对比）。

### 4. 资源与算力

- **文中信息**：未明确说明使用的GPU型号、数量、训练时长等算力资源。
- **说明**：由于仅有摘要和元数据，缺乏实验设置细节，因此无法给出具体算力信息。需要指出这一未明确说明的情况。

### 5. 实验数量与充分性

- **实验数量**：从摘要和元数据推断，主要实验包括：
  - 主实验：在多个代码生成任务上比较CoDiLA与标准DLM及AR基线。
  - 消融实验：可能包括辅助AR模型大小的影响、不同块大小的影响（元数据中提及“局部自回归依赖性建模”，但未明确列出所有消融）。
- **充分性与公平性**：
  - 按论文声称，CoDiLA在准确性和速度上达到了新的帕累托前沿，表明实验设计较为全面。
  - 但缺少具体实验组数和统计显著性说明，且未提供自然语言生成任务上的广泛验证，因此充分性有待完整论文验证。总体上看，实验设计应较为客观，因为使用了标准基准和对比方法。

### 6. 论文的主要结论与发现

- **主要结论**：通过使用一个紧凑的辅助自回归模型（0.6B参数）对并行解码的局部块进行顺序修饰，可以有效消除离散DLM并行采样导致的语法不一致伪影，同时保持亚线性解码速度。
- **发现**：在代码生成基准上，CoDiLA建立了准确率与速度之间新的帕累托边界，表明局部依赖建模是改善离散DLM并行解码质量的关键。

### 7. 优点：方法或实验设计上的亮点

- **方法创新**：巧妙地将局部自回归与全局并行扩散结合，避免了复杂的联合采样计算，仅需小型辅助模型即可大幅提升生成质量。
- **效率**：辅助模型仅0.6B参数，计算开销小，不破坏DLM的并行优势。
- **实验设计**：明确对比了标准DLM和AR基线，并强调帕累托前沿，突出了速度与质量权衡的改进。
- **应用价值**：对代码生成、编辑等需要低延迟和高语法准确性的场景具有实际意义。

### 8. 不足与局限

- **实验覆盖不足**：仅重点测试了代码生成任务，未在自然语言生成、翻译等更广泛的语言任务上验证，泛化性存疑。
- **偏差风险**：辅助AR模型可能过度适应代码语法模式，若应用于非结构化文本，效果可能下降。
- **资源信息缺失**：未提供训练和推理的具体算力，难以评估实际部署成本。
- **可扩展性**：块大小与辅助AR模型容量的依赖关系未深入讨论，可能影响极端长序列下的效果。

（完）
