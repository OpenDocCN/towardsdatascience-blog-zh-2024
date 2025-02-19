# 地理位置编码器

> 原文：[`towardsdatascience.com/geographic-position-encoders-59dafebf6f2d?source=collection_archive---------10-----------------------#2024-10-01`](https://towardsdatascience.com/geographic-position-encoders-59dafebf6f2d?source=collection_archive---------10-----------------------#2024-10-01)

## 理解现代技术如何在神经网络中编码地理坐标

[](https://medium.com/@crastoru?source=post_page---byline--59dafebf6f2d--------------------------------)![Ruth Crasto](https://medium.com/@crastoru?source=post_page---byline--59dafebf6f2d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--59dafebf6f2d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--59dafebf6f2d--------------------------------) [Ruth Crasto](https://medium.com/@crastoru?source=post_page---byline--59dafebf6f2d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--59dafebf6f2d--------------------------------) ·10 分钟阅读·2024 年 10 月 1 日

--

![](img/5d581953d4a0e7b66067889fdc282b58.png)

图片由 [CHUTTERSNAP](https://unsplash.com/@chuttersnap?utm_source=medium&utm_medium=referral) 提供，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

机器学习中的归纳偏差是基于某些先验知识对模型施加的约束。作为人类，我们可以识别出飞翔在天空中的鸟或栖息在树上的鸟。此外，我们不需要检查每一朵云或完全观察整棵树，就能知道我们正在看的是鸟而不是其他东西。这些在视觉过程中的偏差通过两个属性在卷积层中编码：

+   **权重共享**：相同的卷积核权重在输入通道的整个宽度和高度上被重复使用。

+   **局部性**：卷积核的宽度和高度远小于输入。

我们还可以通过选择模型的输入特征来编码归纳偏差，这可以看作是对模型本身的约束。为神经网络设计输入特征涉及表达能力和归纳偏差之间的权衡。一方面，我们希望允许模型具备灵活性，学习超出人类能够检测和编码的模式。另一方面，若没有任何归纳偏差的模型将难以学到任何有意义的内容。

在本文中，我们将探讨设计有效位置编码器的归纳偏差，特别是在地理坐标上的应用。地球上的位置可以作为广泛预测任务的有用输入，包括图像分类。正如我们将看到的，直接使用纬度和经度作为输入特征是约束不足的，最终会使模型更难学习有意义的内容。相反，更常见的做法是将纬度和经度的先验知识编码为一种非参数化的重新映射，我们称之为位置编码器。

# 介绍：变换器中的位置编码器

为了更广泛地说明选择有效位置编码器的重要性，我们首先来看看变换器模型中著名的位置编码器。我们从一个观点开始：传递给注意力模块的标记输入的表示应该包含一些关于它在所属序列中位置的信息。问题是：我们应当如何将位置索引（0, 1, 2…）编码为一个向量？

假设我们有一个与位置无关的标记嵌入。一种可能的方法是将索引值直接加到这个嵌入向量中或与之连接。以下是为什么这种方法效果不佳的原因：

1.  两个嵌入之间的相似性（点积）——在它们的位置被编码之后——应当独立于序列中标记的总数。序列的最后两个标记无论序列长度是 5 个单词还是 50 个单词，它们应该记录相同的相似性。

1.  两个标记之间的相似性不应该依赖于它们位置的绝对值，而只应依赖于它们之间的相对距离。即使编码的索引被归一化到[0, 1]的范围内，位置为 1 和 2 的相邻两个标记也会记录比序列中稍后位置的相同两个标记更低的相似性。

原始的《Attention is All You Need》论文[[1]](https://arxiv.org/pdf/1706.03762)提出的做法是将位置索引*pos*编码为*k*个不同正弦波的离散“快照”，其中*k*是标记嵌入的维度。这些快照计算如下：

![](img/a9556872f6edc6eda258a8d68f95decf.png)

其中 *i =* 1, 2, …, *k* / 2\. 然后，得到的*k*维位置嵌入将按元素与对应的标记嵌入相加。

这种编码的直觉是，任何两个嵌入的快照相位差越大，它们对应位置的距离就越远。两个不同位置的绝对值不会影响它们快照的相位差。此外，由于任何正弦波的范围是[-1, 1]，位置嵌入的幅度不会随着序列长度的增加而增长。

我不会进一步详细讨论这个特定的定位编码器，因为已经有几篇优秀的博客文章做了深入讲解（见 [[2]](https://kazemnejad.com/blog/transformer_architecture_positional_encoding/)）。希望你现在可以理解，一般来说，仔细考虑如何编码位置为什么是如此重要。

# 地理位置编码器

现在让我们转向地理位置编码器。我们希望训练一个神经网络，根据地球表面上的位置预测某些感兴趣的变量。我们该如何将球面坐标系中的位置（λ, ϕ）——即经度/纬度对——编码为一个向量，以便将其作为我们网络的输入？

![](img/1b247402a9bb764ff5a8cfafb2529c4f.png)

由 Peter Mercator 编著， [公有领域](https://commons.wikimedia.org/w/index.php?curid=12226167)。

## 简单方法

一种可能的方法是直接使用纬度和经度值作为输入。在这种情况下，我们的输入特征空间将是矩形 [-*π, π*] × [0*, π*]，我将其称为纬度/经度空间。与变换器的定位编码器类似，这种简单的方法不幸的是也有其局限性：

1.  注意，当你向两极移动时，地球表面上由 1 单位经度 (λ) 覆盖的距离会减小。纬度/经度空间并不能保持地球表面上的距离。

1.  注意，地球上与坐标（λ, ϕ）对应的位置应与坐标（λ + 2*π，ϕ）对应的位置相同。但在纬度/经度空间，这两个坐标之间相距甚远。纬度/经度空间并不能保持周期性：即球面坐标如何在地球表面上环绕。

为了直接从纬度/经度空间的输入中学习有意义的内容，神经网络必须学会如何自行编码关于地球表面曲率的这些属性——这是一个具有挑战性的任务。那么，我们该如何设计一个已经编码了这些归纳偏置的定位编码器呢？让我们探索一下早期的解决方案，以及它们是如何随着时间的推移演变的。

# 早期的定位编码器

## 基于离散化的方法（2015 年）

第一篇提出将地理坐标特征化并作为卷积神经网络输入的论文名为《通过位置上下文改善图像分类》[[3]](https://arxiv.org/pdf/1505.03873)。该论文于 2015 年发表，提出并评估了多种不同的特征化方法，旨在训练更好的地理标记图像分类模型。

它们每种方法背后的理念是将地球上的位置直接编码为一组可以从辅助数据源计算得出的数值特征。一些例子包括：

+   将美国划分为均匀间隔的网格，在纬度/经度空间中使用独热编码将给定位置编码为一个向量，依据其所在的网格来确定。

+   查找与给定位置对应的美国邮政编码，然后从美国社区调查（ACS）中检索关于该邮政编码的人口统计数据，涉及年龄、性别、种族、居住条件等。使用独热编码将这些数据转化为数值向量。

+   对于一组选择的 Instagram 标签，统计不同距离给定位置的标签数量，并将这些计数值连接成一个向量。

+   从 Google Maps 中检索颜色编码地图，用于表示降水、土地覆盖、国会选区等各种特征，并将每个特征的数值颜色值连接成一个向量。

请注意，这些位置编码不是连续的，并且不保留地球表面上的距离。在第一个例子中，两个邻近的地点如果落入不同的网格，它们在特征空间中的距离与两个位于国家两端的地点相等。此外，这些特征大多依赖于辅助数据源的可用性，必须精心设计，并需要特定的标签、地图特征、调查数据等选择。这些方法无法很好地推广到地球上任意位置。

## WRAP (2019)

在 2019 年，一篇名为“仅使用位置的地理先验进行细粒度图像分类”的论文[[4]](https://arxiv.org/pdf/1906.05272)迈出了通向今天广泛使用的地理位置编码器的重要一步。与上一节的工作类似，这篇论文研究了如何利用地理坐标来改进图像分类模型。

他们的位置编码器背后的关键思想是利用正弦和余弦函数的周期性来编码地理坐标如何在地球表面上环绕。给定纬度和经度（λ，ϕ），都被归一化到[-1, 1]范围内，WRAP 位置编码器定义如下：

![](img/920ea42f1458310b33c5bf742712d519.png)

与上一节中的方法不同，WRAP 是连续的，并且可以轻松计算地球上任何位置的值。然后，论文通过实验证明，在这些特征上训练一个全连接网络，并将它们与潜在图像特征结合，可以在细粒度图像分类基准测试中提高性能。

# 双重傅里叶球面方法

WRAP 编码器看起来简单，但它成功地编码了关于地理位置的一个关键归纳偏差，同时保持了表达性和灵活性。为了理解为什么这种位置编码器选择如此强大，我们需要理解双重傅里叶球面（DFS）方法[[5]](https://en.wikipedia.org/wiki/Double_Fourier_sphere_method)。

DFS 是一种方法，它将定义在单位球面上的任何实值函数*f*（*x*，*y*，*z*）转换为在矩形[-*π*，*π*] × [-*π*，*π*]上定义的 2*π*周期函数。从高层次来看，DFS 包括两个步骤：

1.  使用球面坐标重新参数化函数*f*（*x*，*y*，*z*），其中（λ，ϕ）∈ [-*π*，*π*] × [0*，π*]

![](img/61081c492c27cdcfaa8390d55d660dfc.png)

2\. 基于重新参数化的* f*（本质上是“在其上翻倍”）在矩形[-*π*，*π*] × [-*π*，*π*]上定义一个新的分段函数。

请注意，地球表面的 DFS 重新参数化（步骤 1）保持了我们之前讨论的性质。首先，当ϕ趋近于 0 或±*π*（地球的极点）时，经过重新参数化后，两点（λ，ϕ）和（λ'，ϕ）之间的距离会减小。此外，重新参数化是周期性的并且平滑的。

## 傅里叶定理

任何连续的、周期性的实值函数都可以表示为正弦和余弦的加权和，这是一个事实。这个理论叫做傅里叶定理，这种加权和表示形式叫做傅里叶级数。事实证明，任何经过 DFS 变换的函数都可以用有限的正弦和余弦表示。它们被称为**DFS 基函数**，如下所示：

![](img/17444feab234a748fcd0872fe182820b.png)

这里，∪表示集合的并集，*S*是正弦波的尺度（即频率）集合。

## 基于 DFS 的位置编码器

请注意，DFS 基函数集包含了 WRAP 位置编码器中的四项。“Sphere2Vec”[[6]](https://arxiv.org/pdf/2201.10489)是最早观察到这一点的论文，提出了基于 DFS 的统一位置编码器视角。实际上，考虑到这种泛化，我们可以通过选择 DFS 基函数的任何子集来构建一个地理位置编码器——WRAP 仅仅是其中的一种选择。想了解更多，可以参考[[7]](https://arxiv.org/pdf/2310.06743v2)中对各种基于 DFS 的位置编码器的全面概述。

## 为什么基于 DFS 的编码器如此强大？

考虑在基于 DFS 的位置编码器上训练一个线性层时会发生什么：网络的每个输出元素都是所选 DFS 基函数的加权和。因此，网络可以被解释为**学习到的傅里叶级数**。由于几乎任何定义在球面上的函数都可以使用 DFS 方法进行变换，因此可以推断，在线性层上训练的 DFS 基函数足够强大，能够在球面上编码任意函数！这类似于多层感知器的通用逼近定理。

在实际应用中，仅使用 DFS 基函数的一个小子集用于位置编码器，并在其上训练一个全连接网络。非参数化位置编码器与神经网络的组合通常被称为**位置编码器**：

![](img/315f844ae0997de2f4526c4cbc711b3d.png)

地理位置编码器的示意图。图片来自作者。

# 当前的地理位置编码器

如我们所见，基于 DFS 的位置编码器可以有效地编码我们对于地球表面曲率的归纳偏见。基于 DFS 的编码器的一个局限性是它们假设矩形域[-*π*，*π*] × [-*π*，*π*]。虽然这个假设通常是可以接受的，因为 DFS 重新参数化已经考虑到距离在接近极地时如何发生扭曲，但这个假设在极地本身（ϕ = 0，± *π*）的地方会失效，因为这些点在矩形域中是线段，而在地球表面则会塌缩成单一的点。

一种名为球面调和函数的不同基函数最近成为了一种替代方案。球面调和函数是本地定义在球面上的基函数，而非矩形区域。与基于 DFS 的编码器相比，它们已被证明在地球极地区域展现出较少的伪影[[7]](https://arxiv.org/pdf/2310.06743v2)。值得注意的是，球面调和函数是 SatCLIP 位置编码器[[8]](https://arxiv.org/pdf/2311.17179)中使用的基函数，SatCLIP 是一个近期的地理坐标基础模型，以 CLIP 的风格进行训练。

尽管地理位置编码器在 2010 年代初期始于离散的、手工设计的特征，但这些特征并不容易推广到任意位置，并且需要特定领域的元数据，如土地覆盖和人口统计数据。今天，地理坐标作为神经网络输入变得更为普遍，因为已有简单但有意义且富有表现力的编码方式出现。随着网络规模数据集的崛起，这些数据集通常带有地理标签，使用地理坐标作为预测任务的输入的潜力如今巨大。

# 参考文献

[1] A. Vaswani, N. Shazeer, N. Parmar, J. Uszkoreit, L. Jones, A. N. Gomez, L. Kaiser & I. Polosukhin, [Attention Is All You Need](https://arxiv.org/pdf/1706.03762) (2017), 第 31 届神经信息处理系统大会

[2] A Kazemnejad, [Transformer Architecture: The Positional Encoding](https://kazemnejad.com/blog/transformer_architecture_positional_encoding/) (2019), Amirhossein Kazemnejad’s Blog

[3] K. Tang, M. Paluri, L. Fei-Fei, R. Fergus, L. Bourdev, [Improving Image Classification with Location Context](https://arxiv.org/pdf/1505.03873) (2015)

[4] O. Mac Aodha, E. Cole, P. Perona, [Presence-Only Geographical Priors for Fine-Grained Image Classification](https://arxiv.org/pdf/1906.05272) (2019)

[5] [双重傅里叶球面方法](https://en.wikipedia.org/wiki/Double_Fourier_sphere_method)，维基百科

[6] G. Mai, Y. Xuan, W. Zuo, K. Janowicz, N. Lao [Sphere2Vec: Multi-Scale Representation Learning over a Spherical Surface for Geospatial Predictions](https://arxiv.org/pdf/2201.10489) (2022)

[7] M. Rußwurm, K. Klemmer, E. Rolf, R. Zbinden, D. Tuia, [Geographic Location Encoding with Spherical Harmonics and Sinusoidal Representation Network](https://arxiv.org/pdf/2310.06743v2) (2024), ICLR 2024

[SatCLIP: 全球通用的卫星图像位置嵌入](https://arxiv.org/pdf/2311.17179)（2024）由 K. Klemmer, E. Rolf, C. Robinson, L. Mackey, M. Rußwurm 编写。
