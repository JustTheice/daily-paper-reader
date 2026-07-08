---
title: Multimodal Latent Language Modeling with Next-Token Diffusion
title_zh: 多模态潜在语言建模与Next-Token扩散
authors: "Yutao Sun, Hangbo Bao, Wenhui Wang, Zhiliang Peng, Li Dong, Shaohan Huang, Yaoyao Chang, Jianyong Wang, Furu Wei"
date: 2026-04-30
pdf: "https://openreview.net/pdf/1a5d94b2b364b52bbc9b44ce0db4991b065c79ff.pdf"
tags: ["query:diffusion-lm"]
score: 6.0
evidence: 多模态潜在语言建模中的next-token扩散
tldr: 该工作提出潜在语言模型（LatentLM），通过VAE将连续数据编码为潜在向量，并采用next-token扩散进行自回归生成，统一处理文本、图像等多模态数据。虽然涉及扩散，但其生成方式本质上是自回归的，与需求中的非自回归离散扩散语言模型方法论上有差异，可作为方法学桥梁。
source: ICML-2026-Accepted
selection_source: conference_retrieval
motivation: 现有生成模型难以统一处理离散和连续模态。
method: 使用VAE和next-token扩散在一个因果Transformer中建模多模态。
result: 在图像生成等任务上与强基线竞争。
conclusion: next-token扩散为多模态统一建模提供了有效框架。
---

## Abstract
Multimodal generative models require a unified approach to handle both discrete data (e.g., text and code) and continuous data (e.g., image, audio, video). In this work, we propose Latent Language Modeling (LatentLM), which seamlessly integrates continuous and discrete data using causal Transformers. Specifically, we employ a variational autoencoder (VAE) to represent continuous data as latent vectors and introduce next-token diffusion for autoregressive generation of these vectors. Additionally, we develop $\sigma$-VAE to address the challenges of variance collapse, which is crucial for autoregressive modeling. Extensive experiments demonstrate the effectiveness of LatentLM across various modalities. In image generation, LatentLM sis competitive with or outperforms DiT-style baselines under matched unified settings. When integrated into multimodal large language models, LatentLM provides a general-purpose interface that unifies multimodal generation and understanding. Experimental results show that LatentLM achieves favorable performance compared to Transfusion and vector quantized models in the setting of scaling up training tokens. In text-to-speech synthesis, LatentLM outperforms the state-of-the-art VALL-E 2 model in speaker similarity and robustness, while requiring 10 fewer decoding steps. The results establish LatentLM as a highly effective and scalable approach to advance large multimodal models.

---

## 论文详细总结（自动生成）

### 1. 论文的核心问题与整体含义（研究动机和背景）

- **核心问题**：现有生成模型难以在统一的框架下处理离散数据（如文本、代码）和连续数据（如图像、音频、视频）。传统的自回归模型擅长离散序列生成，而扩散模型则更适用于连续域，二者难以兼顾。
- **整体含义**：本文提出**潜在语言建模（LatentLM）**，旨在通过一个统一的因果Transformer架构，无缝整合离散与连续模态的生成与理解，为构建大规模多模态模型提供高效且可扩展的解决方案。

### 2. 论文提出的方法论：核心思想、关键技术细节、公式或算法流程（文字说明）

- **核心思想**：利用变分自编码器（VAE）将连续数据（如图像、音频）压缩为连续的潜在向量，然后引入**next-token扩散**机制，以自回归方式逐块生成这些潜在向量。对于离散数据（如文本），直接使用标准交叉熵损失。整体模型通过因果Transformer统一处理两种模态。
- **关键技术细节**：
    - **VAE编码**：将连续数据编码为固定维度的潜在向量序列，每个向量代表一个“token”。
    - **Next-token扩散**：在自回归生成过程中，每一步预测下一个潜在向量；与标准分类不同，这里采用连续扩散过程，从噪声逐步去噪得到目标向量。
    - **σ-VAE**：针对方差坍塌（variance collapse）问题进行了改进，确保潜在空间具有足够表达能力，从而支持有效的自回归建模。
- **算法流程（文字说明）**：
    1. 训练一个VAE将连续数据映射到潜在空间。
    2. 在因果Transformer中，输入序列由离散token和连续潜在向量混合组成。
    3. 对于连续部分，每个位置使用next-token扩散目标（如预测噪声或原始向量），对于离散部分使用交叉熵。
    4. 生成时：自回归地依次预测下一个token，若为连续潜在向量则通过扩散采样逐步去噪得到向量，再通过VAE解码器还原为原始数据。

### 3. 实验设计：数据集/场景、benchmark、对比方法

- **图像生成**：在标准图像生成benchmark（如ImageNet）上与**DiT-style**基线对比（在统一设置下）。
- **多模态大语言模型（MLLM）**：集成LatentLM作为通用接口，统一生成与理解任务。对比方法包括**Transfusion**（类似混合训练方法）和**向量量化模型**（如VQ-VAE等），在扩展训练tokens的设定下评估。
- **文本转语音（TTS）**：语音合成任务，对比当前最优方法**VALL-E 2**，评估指标包括说话人相似度与鲁棒性（如词错误率或自然度）。
- **数据集**：未在摘要中详细列出，但推断包括大规模图像数据集、多模态指令数据、语音语料库等。

### 4. 资源与算力

- **未明确说明**：论文摘要及元数据中未提及具体的GPU型号、数量或训练时长。因此无法提供精确算力信息。通常此类多模态大规模实验可能使用数十到数百张A100或H100 GPU，但本文未公开细节。

### 5. 实验数量与充分性

- **实验数量**：文中提及三大类实验（图像生成、多模态LLM、TTS），每个大类下又有多个子任务（如不同分辨率、不同规模模型）以及与多个基线的对比。虽然未列出全部消融实验，但摘要中提到了“extensive experiments”。
- **充分性与公平性**：
    - 在图像生成中，与DiT-style基线“在匹配的统一设置下”比较，说明控制变量，较为公平。
    - 在多模态LLM实验中，与Transfusion和VQ模型在“scaling up training tokens”条件下比较，也体现了公平性。
    - 在TTS中，与SOTA模型VALL-E 2对比，并指出解码步数减少10倍，结果指标优越。
    - **不足**：消融实验细节（如σ-VAE的影响、next-token扩散超参数等）未在摘要中体现，可能论文正文有更详细分析，但此处信息不充分。

### 6. 论文的主要结论与发现

- **LatentLM在图像生成上与DiT风格基线竞争或超越**，证明连续潜在扩散自回归方案的有效性。
- **集成到多模态大语言模型中**，LatentLM作为统一接口，在生成与理解任务上优于Transfusion和向量量化模型（在扩大训练tokens时）。
- **在文本转语音任务上**，LatentLM超越VALL-E 2，说话人相似度和鲁棒性更优，且解码步数减少10倍（高效性）。
- **整体结论**：Next-token扩散为多模态统一建模提供了有效框架，LatentLM是一种高效且可扩展的方法，可推进大规模多模态模型的发展。

### 7. 优点：方法或实验设计上的亮点

- **方法创新点**：
    - **Next-token扩散**将连续潜在空间的自回归生成与扩散过程巧妙结合，既保留了因果顺序，又避免了对离散量化带来的信息损失。
    - **σ-VAE**解决了方差坍塌问题，使得潜在向量适合自回归建模，增强了稳定性和表征能力。
    - **统一架构**：用单一的因果Transformer同时处理离散和连续模态，简化了多模态系统的设计。
- **实验设计亮点**：
    - 覆盖了图像、文本、语音等多种模态，验证了方法的通用性。
    - 与多个强基线（DiT、Transfusion、VQ模型、VALL-E 2）对比，且控制设置公平。
    - 在TTS上特别强调解码效率提升（10倍），突出了实际部署优势。

### 8. 不足与局限

- **实验覆盖**：摘要中未提供消融实验、潜在维度选择、扩散步数等关键参数的影响分析，这些对于理解方法鲁棒性很重要（论文正文可能包含，但此处缺失）。
- **偏差风险**：仅列举了LatentLM优于基线的情况，未讨论可能存在的失败案例或性能低于基线的场景（如小规模数据或低资源模态）。
- **应用限制**：
    - VAE编码器的引入增加了系统复杂度，且潜在空间的质量直接影响生成效果。
    - Next-token扩散的自回归性质仍会带来逐块生成的速度瓶颈，虽然TTS上比VALL-E 2更快，但相比于并行生成方法仍有限制。
    - 对于离散与连续数据混合的序列长度管理（如图像潜在向量数量）需要手动设计，可能影响扩展性。
- **资源公开不足**：未披露训练算力与成本，不利于其他研究者复现或评估可扩展性。

---

（完）
