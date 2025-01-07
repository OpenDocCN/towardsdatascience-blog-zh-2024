# 超越注意力机制：先进的位置嵌入方法如何在Transformer架构中改进原始方法

> 原文：[https://towardsdatascience.com/beyond-attention-how-advanced-positional-embedding-methods-improve-upon-the-original-transformers-90380b74d324?source=collection_archive---------3-----------------------#2024-10-29](https://towardsdatascience.com/beyond-attention-how-advanced-positional-embedding-methods-improve-upon-the-original-transformers-90380b74d324?source=collection_archive---------3-----------------------#2024-10-29)

## 从正弦波到RoPE和ALiBi：先进的位置编码如何克服Transformer中的局限性

[](https://medium.com/@InfiniteLearningLoop?source=post_page---byline--90380b74d324--------------------------------)[![Elahe Aghapour](../Images/47a2023c566d50d8ecfcafdb69bb9bb7.png)](https://medium.com/@InfiniteLearningLoop?source=post_page---byline--90380b74d324--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--90380b74d324--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--90380b74d324--------------------------------) [Elahe Aghapour](https://medium.com/@InfiniteLearningLoop?source=post_page---byline--90380b74d324--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--90380b74d324--------------------------------) ·阅读时间：9分钟·2024年10月29日

--

**作者：** [**Elahe Aghapour**](https://medium.com/u/75214fb27311?source=post_page---user_mention--90380b74d324--------------------------------)**，** [**Salar Rahili**](https://medium.com/u/6dff1eb2cc9f?source=post_page---user_mention--90380b74d324--------------------------------)

# 引言：

近年来，模型的指数级进展与Transformer架构的出现密切相关。以前，人工智能科学家需要为每个任务选择架构，然后优化超参数以获得最佳性能。另一个限制其潜力的挑战是处理数据长程依赖性的困难，导致了梯度消失、长序列中上下文丢失的问题，以及由于局部性约束无法捕获全局上下文。此外，传统模型缺乏可扩展性和并行化，减缓了大数据集的训练进程，阻碍了该领域的进展。

Transformer架构通过其自注意力机制解决了这些问题，彻底改变了这一领域。它使得模型能够捕捉长序列之间的关系，并高效地理解全局上下文，同时具有高度的并行化能力，并能在各种模态下适应，例如文本、图像等。在自注意力机制中，对于每个令牌，它的查询与所有其他令牌的键进行比较，以计算相似度得分。这些相似度然后用于加权值向量，最终决定当前令牌应该关注哪里。自注意力将所有令牌视为同等重要，而不考虑它们的顺序，丧失了关于令牌出现顺序的关键信息，换句话说，它将输入数据视为没有顺序的集合。现在，我们需要一种机制来强制在数据中施加某种顺序的概念，因为自然语言和许多其他类型的数据本质上是顺序性的和位置敏感的。这就是位置嵌入发挥作用的地方。位置嵌入编码了每个令牌在序列中的位置，使模型能够保持对序列结构的意识。已经探索了多种编码位置信息的方法，我们将在本博客文章中讨论这些方法。

![](../Images/998a5ddc31efe8bb315ebe9eab6c558c.png)

由DALL-E生成的图像

# **注意力机制：**

设*S = {wi}*，其中*i = 1, …, N*，表示一个由*N*个输入令牌组成的序列，其中*wi*表示第*i*个令牌。因此，*S*的对应令牌嵌入可以表示为*E = {xi}*，其中*i = 1, …, N*，*xi*是第*i*个令牌*wi*的*d*维令牌嵌入向量。自注意力机制将位置嵌入融入令牌嵌入，并生成查询、键和值的表示形式，如下所示：

![](../Images/764b45fac9c35b4504c2338f65a6995b.png)

然后，注意力权重根据查询和键向量之间的相似度计算：

![](../Images/6d9f343c6adfcbfcddf0bfb8dc532620.png)

注意力权重决定了令牌*n*对令牌*m*的重要性。换句话说，就是令牌*m*应该给予令牌*n*多少注意力。令牌*m*的输出是作为值向量的加权和计算的：

![](../Images/5303090e8fe3e310ecf8ee7321a4e822.png)

因此，注意力机制使得令牌*m*能够从序列中的其他令牌收集信息。

![](../Images/5d1d99e883ab4f86c0d399614800f9ef.png)

图1. Transformer架构中的位置编码（图像来自[论文](https://arxiv.org/pdf/1706.03762)）。

# **1. 绝对位置嵌入：**

方程（1）的一个典型选择是：

![](../Images/1009b706a42c879f4af02733f5d9c422.png)

其中*pi*是一个*d*维向量，表示令牌*xi*的绝对位置。正弦位置编码和学习的位置编码是生成*pi*的两种替代方法。

## **1.a 正弦位置编码**

正弦位置编码在“[Attention is all you need](https://arxiv.org/pdf/1706.03762)”论文中被引入，提出了Transformer架构。正弦位置编码为输入序列中的每个token提供了一个独特的位置表示。它基于具有不同频率的正弦和余弦函数，如下所示：

![](../Images/108d3fd539207a2e549a52e501c3172e.png)

其中，*pos*是token在序列中的位置，*d*是位置嵌入的维度，i是维度索引（*0<=i<d*）。

正弦位置编码中使用的正弦和余弦函数与**傅里叶变换**有着深刻的关系。通过使用不同频率的范围来编码位置，Transformer创建了一个类似于傅里叶变换的表示，其中：

+   **高频成分**（较低的*i*）使模型能够捕捉到局部位置的关系。这对于理解序列中相邻token之间的关系非常有用，例如词对。

+   **低频成分**（较高的*i*）捕捉整个序列中的更全局的模式。这有助于模型关注可能相距较远的token之间的广泛关系，例如两句话之间的依赖关系。

这有助于模型通过比较位置编码来理解token之间的相对位置。正弦位置编码不需要额外的训练参数，并且在推理时能泛化到更长的序列长度。然而，它的表现力有限。

## **1.b 学习型位置编码**

学习型位置编码在“[Attention is all you need](https://arxiv.org/pdf/1706.03762)”论文中被引入，并在[BERT](https://arxiv.org/pdf/1810.04805)和[GPT](https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf)模型中作为正弦位置编码的替代方案。对于学习型位置编码，序列中的每个位置（例如，第一个token，第二个token等）都会分配一个嵌入向量。这些位置嵌入在训练过程中与其他Transformer参数一起学习。例如，如果模型的上下文长度为512，token嵌入的维度为768（即*d*=768），则会将一个大小为512*768的可学习张量加入到其他可训练参数中。这意味着模型逐渐学习如何为特定任务（如文本分类或翻译）编码位置信息。

学习型位置嵌入比正弦位置嵌入更具表现力，因为模型可以学习一个特定任务有效的位置嵌入。然而，它们引入了更多的可训练参数，从而增加了模型的大小和计算成本。

# **2. 相对位置嵌入**

正弦型和学习型位置编码侧重于标记的绝对位置。然而，注意力机制是通过计算其他标记对每个特定标记的重要性来工作的。因此，这一过程依赖于标记的相对位置（它们之间的距离），而不是标记的绝对位置。为了解决绝对位置嵌入的局限性，引入了相对位置编码。

[RelativePosEmb](https://arxiv.org/pdf/1803.02155) 不将位置信息添加到标记嵌入中。相反，它修改了每一层计算键（key）和值（value）的方法，如下所示：

![](../Images/72e5b379418a7f719d2e2f6566b6c4bd.png)

这里，*r = clip(m-n, Rmin, Rmax)* 表示位置 m 和 n 之间的相对距离。最大相对位置会被裁剪，假设超出某个距离后的精确相对位置没有用处。裁剪最大距离使得模型在推理时能够外推，即能够推广到训练时未见过的序列长度。然而，这种方法可能会丢失一些来自标记绝对位置的有用信息（例如第一个标记的位置）。

你可能会注意到 *fq* 缺少位置编码。这是因为我们正在编码相对位置。在注意力公式中，查询（query）和值（key）用于计算注意力权重，如公式（2）所示，因此我们只需要查询或键之一来包含相对位置编码。

这种编码已在许多模型中使用，如 [Transformer-XL](https://arxiv.org/pdf/1901.02860) 和 [T5](https://www.jmlr.org/papers/volume21/20-074/20-074.pdf)。在应用相对位置编码时，有不同的替代方法，可以在文献 [[7]](https://www.jmlr.org/papers/volume21/20-074/20-074.pdf) 和 [[8]](https://arxiv.org/pdf/2006.03654) 中找到。

# **3\. 旋转位置嵌入（RoPE）**

与以往的方法不同，[RoPE](https://arxiv.org/pdf/2104.09864)基于标记的位置，在多维空间中旋转向量。它不是将位置信息添加到标记嵌入中，而是修改了每一层计算注意力权重的方式，如下所示：

![](../Images/2c2c747a497c0ec921bc2dc550a8ac7f.png)

他们提出了一个通用的旋转矩阵，适用于任何偶数维度的嵌入 *d*，公式如下：

![](../Images/a9b8dc8e3f08c5dbc3709a5c8eef9607.png)

其中，*θi* 是预定义的：

![](../Images/e7d44151cb5fc03ca784f80c9665741b.png)

将 [RoPE](https://arxiv.org/pdf/2104.09864) 应用到注意力权重中得到：

![](../Images/6d510a7dbb26896bd120fdcbb45ff781.png)

请注意，[RoPE](https://arxiv.org/pdf/2104.09864)的公式并未将位置信息添加到注意力模块的值中。注意力模块的输出是值向量的加权和，由于位置信息没有添加到值中，每个 Transformer 层的输出没有显式的位置信息。

像[LLaMA](https://arxiv.org/pdf/2302.13971)和[GPT-NeoX](https://arxiv.org/pdf/2204.06745#cite.su2021roformer)这样的流行模型正在使用[RoPE](https://arxiv.org/pdf/2104.09864)。

![](../Images/042b42f1c09ae586cd90a211c1723857.png)

图2：[ALiBi](https://arxiv.org/pdf/2108.12409)方法的可视化（图像来源于[论文](https://arxiv.org/pdf/2108.12409)）。

# **4. 线性偏置注意力（ALiBi）**

[ALiBi](https://arxiv.org/pdf/2108.12409)也不将位置编码添加到词嵌入中；相反，它通过对注意力权重得分施加一个与标记之间距离成比例的惩罚。因此，两个标记i和j之间的注意力得分在每一层的计算方式如下：

*Attention score = query_i . key_j — m.(i-j)*

其中，*-m.(i-j)*是一个惩罚项，与标记*i*和*j*之间的距离成正比。标量*m*是一个特定于头部的斜率，在训练前就已固定，其值对于不同的头部会按照几何序列选择。例如，对于8个头部，*m*可能是：

![](../Images/7a0c79a5fea18ba0aabfa8f9b196834a.png)

这意味着，第一个头有相对较大的*m*值，因此它对远离的标记惩罚更多，专注于最近的标记，而第八个头有最小的*m*值，允许它关注更远的标记。图2也提供了可视化展示。

ALiBi被用于[BloombergGPT](https://arxiv.org/pdf/2303.17564)和[BLOOM](https://inria.hal.science/hal-03850124v1/document)。

# **推理时的Transformer外推：**

Transformer推理时的外推能力指的是模型在输入序列比训练时的序列更长的情况下，仍然能够良好地表现。Transformer机制对输入长度是不可知的，这意味着在推理时，它可以处理更长的序列。然而，需要注意的是，计算成本随着输入长度的增加呈二次增长，即使Transformer层本身对输入长度是不可知的。

[ALiBi](https://arxiv.org/pdf/2108.12409)的作者证明了，transformer外推的瓶颈在于其位置编码方法。如图3所示，他们比较了不同位置编码方法的外推能力。由于学习到的位置编码无法编码比训练长度更长的位置，因此它没有外推能力。

![](../Images/463e1fb96f99aff8dbb3dd6fad4ab1f4.png)

图3：外推：随着输入序列变长（x轴），[正弦](https://user.phil.hhu.de/~cwurm/wp-content/uploads/2020/01/7181-attention-is-all-you-need.pdf)、[RoPE](https://arxiv.org/pdf/2104.09864)和[T5](https://www.jmlr.org/papers/volume21/20-074/20-074.pdf)的位置编码表现出困惑度下降（y轴，数值越低越好），而[ALiBi](https://arxiv.org/pdf/2108.12409)则没有（图像来源于[论文](https://arxiv.org/pdf/2108.12409)）。

图3显示了实际应用中，正弦位置嵌入的外推能力非常有限。尽管[RoPE](https://arxiv.org/pdf/2104.09864)优于正弦嵌入，但仍然未能取得令人满意的结果。[T5](https://www.jmlr.org/papers/volume21/20-074/20-074.pdf)偏置方法（相对位置嵌入的一种形式）在外推方面优于正弦嵌入和[RoPE](https://arxiv.org/pdf/2104.09864)嵌入。不幸的是，[T5](https://www.jmlr.org/papers/volume21/20-074/20-074.pdf)偏置在计算上开销较大（见图4）。[ALiBi](https://arxiv.org/pdf/2108.12409)在所有这些位置嵌入中表现最佳，且仅有轻微的（0-0.7%）内存增加。

![](../Images/5de14ba673b2dde6cc992ccadda9135d.png)

图4：批量训练、推理速度和[正弦](https://user.phil.hhu.de/~cwurm/wp-content/uploads/2020/01/7181-attention-is-all-you-need.pdf)、[RoPE](https://arxiv.org/pdf/2104.09864)、[T5](https://www.jmlr.org/papers/volume21/20-074/20-074.pdf)和[ALiBi](https://arxiv.org/pdf/2108.12409)位置编码的比较（图片来自[论文](https://arxiv.org/pdf/2108.12409)）。

# 结论：

总结：在Transformer架构中，位置编码方式显著影响其理解顺序数据的能力，尤其是在推理时的外推能力。虽然绝对位置嵌入方法提供了位置信息，但它们常常难以应对Transformer的外推能力。因此，提出了更新的位置嵌入方法。相对位置编码、RoPE和ALiBi具备在推理时外推的能力。随着Transformer不断在各类应用中被集成，优化位置编码对于提升其性能边界至关重要。

> 本文中表达的观点仅代表我们个人的看法，并不反映我们雇主的立场。

## **参考文献：**

[1] Vaswani, A. “Attention is all you need.” (2017)。

[2] [BERT](https://arxiv.org/pdf/1810.04805)：Devlin, Jacob. “Bert：用于语言理解的深度双向Transformer预训练。” (2018)。

[3] [GPT](https://cdn.openai.com/better-language-models/language_models_are_unsupervised_multitask_learners.pdf)：Radford, Alec 等. “语言模型是无监督的多任务学习者.” (2019)。

[4] [RelativePosEmb](https://arxiv.org/pdf/1803.02155)：Shaw, Peter 等. “带有相对位置表示的自注意力.” (2018)。

[5] [Transformer-XL](https://arxiv.org/pdf/1901.02860) Dai, Zihang. “Transformer-xl：超越固定长度上下文的注意力语言模型。” (2019)。

[6] [T5](https://www.jmlr.org/papers/volume21/20-074/20-074.pdf)：Raffel, Colin 等. “通过统一的文本到文本转换器探索迁移学习的极限。” (2020)。

[7] Raffel, Colin 等. “通过统一的文本到文本转换器探索迁移学习的极限。” (2020)

[8] He, Pengcheng, 等. “Deberta：解码增强的BERT模型，具有解耦注意力。”（2020年）。

[9] [RoPE](https://arxiv.org/pdf/2104.09864): Su, Jianlin, 等. “Roformer：具有旋转位置编码的增强型变换器。”（2024年）。

[10] [LLaMA](https://arxiv.org/pdf/2302.13971): Touvron, Hugo, 等. “Llama：开源且高效的基础语言模型。”（2023年）。

[11] [GPT-NeoX](https://arxiv.org/pdf/2204.06745#cite.su2021roformer): Black, Sid, 等. “Gpt-neox-20b：一个开源自回归语言模型。”（2022年）。

[12] [ALiBi](https://arxiv.org/pdf/2108.12409): Press, Ofir, 等. “训练短，测试长：具有线性偏置的注意力机制能够进行输入长度外推。”（2021年）。

[13] [BloombergGPT](https://arxiv.org/pdf/2303.17564): Wu, Shijie, 等. “Bloomberggpt：用于金融的大型语言模型。”（2023年）。

[14] [BLOOM](https://inria.hal.science/hal-03850124v1/document): Le Scao, Teven, 等. “Bloom：一个具有1760亿参数的开源多语言语言模型。”（2023年）。
