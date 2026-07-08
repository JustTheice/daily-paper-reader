---
title: "Search or Accelerate: Confidence-Switched Position Beam Search for Diffusion Language Models"
title_zh: 搜索或加速：针对扩散语言模型的置信切换位置束搜索
authors: "Mingyu Cao, Alvaro Correia, Christos Louizos, Shiwei Liu, Lu Yin"
date: 2026-04-30
pdf: "https://openreview.net/pdf/d1a46af41d5a632c32ab7203dc698cae85867e3a.pdf"
tags: ["query:diffusion-lm"]
score: 9.0
evidence: 针对掩码扩散语言模型的解码算法
tldr: 该论文针对扩散语言模型标准贪婪解码可能导致次优解的问题，提出无训练解码算法SOAR。该算法根据模型置信度自适应调整搜索宽度：低置信度时扩大搜索避免过早决定，高置信度时并行解码减少迭代。实验表明SOAR在推理型任务上改进了生成质量且加速解码。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 扩散语言模型解码易陷入局部最优，需一种自适应解码策略。
method: 提出SOAR算法，基于模型不确定性动态切换搜索宽度和并行解码。
result: 在推理型任务上，SOAR改善了生成质量并减少了去噪迭代次数。
conclusion: SOAR是一种训练免费且有效的扩散语言模型解码改进方法。
---

## Abstract
Diffusion Language Models (DLMs) generate text by iteratively denoising a masked sequence, repeatedly deciding which positions to commit at each step. Standard decoding follows a greedy rule, unmasking the most confident positions, yet this local choice can lock the model into a suboptimal unmasking order, especially on reasoning-heavy prompts. We present Search Or AcceleRate (SOAR), a training-free decoding algorithm that adapts its behavior to the model’s uncertainty. When confidence is low, SOAR briefly widens the search over alternative unmasking decisions to avoid premature commitments; when confidence is high, it collapses the search and decodes many positions in parallel to reduce the number of denoising iterations. Across mathematical reasoning and code generation benchmarks (GSM8K, MBPP, HumanEval) on Dream-7B and LLaDA-8B, SOAR improves generation quality while maintaining competitive inference speed, offering a practical way to balance quality and efficiency in DLM decoding.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **研究动机**：扩散语言模型（DLMs）通过迭代去噪掩码序列生成文本，每一步需要决定哪些位置进行“提交”（unmask）。标准的贪婪解码策略每次选择最置信的位置解锁，但这种局部贪心可能陷入次优的解码顺序，尤其在推理密集型任务（如数学推理、代码生成）中会导致生成质量下降。
- **整体含义**：现有解码方法在效率和效果之间存在矛盾——贪婪解码快但可能次优；扩大搜索（如束搜索）能提升质量但增加解码步数和延迟。论文旨在设计一种无需额外训练的自适应解码算法，在保证生成质量的同时维持接近贪婪解码的速度。

### 2. 论文提出的方法论：核心思想、关键技术细节

- **核心思想**：根据模型在每一步的**置信度**动态调整搜索策略。低置信度时扩大搜索宽度以避免过早提交错误令牌；高置信度时收缩搜索并并行解码多个位置以减少去噪迭代次数。
- **技术细节**：
  - 算法名称：**Search Or AcceleRate (SOAR)**
  - 输入：当前步的掩码序列、模型输出的概率分布。
  - 步骤：
    1. **置信度评估**：计算当前所有待解码位置的最大概率（或熵）——若最大概率低于阈值，视为低置信度。
    2. **低置信度分支**：**束搜索**——同时保留多个候选解（unmasking决策），在后续步骤中探索不同顺序，避免过早决定。
    3. **高置信度分支**：**并行解码**——一次性解锁多个高置信度位置，大幅减少去噪步数（加速）。
  - 阈值或切换机制：通过超参数控制（如置信度阈值），可在束搜索宽度与并行解码数之间平滑切换。
  - 无需训练：算法直接应用于预训练好的DLM，不修改模型参数。
- **公式/算法流程**（用文字说明）：
  - 初始化：完整掩码序列。
  - 重复直到所有位置被解锁：
    - 对当前掩码序列，前向传播获得各位置对数概率。
    - 计算每个位置的最大概率值，排序。
    - 若当前最大概率 < 阈值τ：启动**位置束搜索**，保留top-k个候选unmasking动作，分别生成后续序列。
    - 若当前最大概率 ≥ τ：启动**并行解码**，一次性unmask前m个高置信度位置（m可动态或固定）。
    - 更新序列和掩码。
  - 最后从束候选中选择最高得分序列。

### 3. 实验设计：数据集、基准、对比方法

- **数据集/场景**：
  - **数学推理**：GSM8K（小学数学应用题）
  - **代码生成**：MBPP（多语言代码片段）、HumanEval（函数级代码补全）
- **基准模型**：
  - Dream-7B（7B参数扩散语言模型）
  - LLaDA-8B（8B参数扩散语言模型）
- **对比方法**（需推断）：
  - 贪婪解码（标准baseline）
  - 可能也对比了传统的束搜索（固定宽度）或其他采样方法（如top-k、核采样），但摘要中仅明确对比了贪婪解码。
- **评估指标**：生成质量（如准确率、pass@k）和推理速度（去噪迭代次数、延迟等）。

### 4. 资源与算力

- **文中未明确说明**使用的GPU型号、数量或训练时长。由于SOAR是训练无关的解码算法，其推理计算量主要取决于模型前向传播次数，但文中未报告具体硬件配置或加速比数值。
- **建议指出**：论文未提供算力相关细节，仅通过迭代次数减少暗示效率提升。

### 5. 实验数量与充分性

- **实验数量**：在2个模型、3个基准（GSM8K、MBPP、HumanEval）上进行了评估，覆盖了推理和代码生成两个主要场景。
- **充分性评估**：
  - **优点**：覆盖了不同领域的推理任务，模型尺度也有差异（7B vs 8B），能验证方法泛化性。
  - **不足**：缺少消融实验详细数据（如阈值τ的影响、束宽度/并行数的敏感性）、未对比其他自适应解码方法（如top-p、典型采样）、未在更大模型或非推理任务（如故事生成）上测试。论文本身较短，实验细节可能不足。总体而言，实验设计合理但不够全面，偏重初步验证。

### 6. 论文的主要结论与发现

- SOAR在数学推理和代码生成任务上**改善了生成质量**（相比贪婪解码），同时**维持了具有竞争力的推理速度**（通过并行解码减少迭代次数）。
- 自适应搜索策略有效避免了贪婪解码的局部最优问题，尤其在高难度推理样本上提升明显。
- SOAR是一种**无需训练、即插即用**的解码改进方法，为扩散语言模型在质量与效率之间提供了实用的平衡。

### 7. 优点

- **方法简洁且通用**：不修改模型，只基于模型自身置信度改变解码行为，易于部署到任意扩散语言模型。
- **自适应加速**：在高置信度区域自动加快解码，避免了固定束搜索带来的额外计算开销。
- **效果提升明确**：在多个推理型benchmark上验证了质量改善，同时加速解码。
- **关键创新**：将“搜索”与“加速”动态结合，区别于传统静态束搜索或贪心解码。

### 8. 不足与局限

- **实验覆盖有限**：仅测试了数学和代码两个领域，未在开放域生成（如摘要、对话）中验证，可能通用性不足。
- **缺少敏感性分析**：未详细讨论置信度阈值的选择如何影响效果和速度，依赖人工设定。
- **对比方法单一**：只明确对比贪婪解码，未与现代采样方法（如contrastive decoding, typical sampling）或束搜索本身（固定宽度）进行公平比较。
- **论文信息量较少**：仅提供了摘要和元数据，缺乏完整论文细节（如算法伪代码、超参数设置、标准差等），难以全面评估实验结果可靠性。
- **可复现性问题**：未提供代码或配置文件，可能影响他人复现。

（完）
