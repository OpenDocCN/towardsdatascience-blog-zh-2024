# 可解释的潜在空间：使用填充空间的向量量化

> 原文：[https://towardsdatascience.com/interpretable-latent-spaces-using-space-filling-vector-quantization-e4eb26691b14?source=collection_archive---------7-----------------------#2024-04-08](https://towardsdatascience.com/interpretable-latent-spaces-using-space-filling-vector-quantization-e4eb26691b14?source=collection_archive---------7-----------------------#2024-04-08)

## 一种新的无监督方法，结合了向量量化和填充空间曲线两个概念，用于解释深度神经网络（DNNs）的潜在空间。

[](https://medium.com/@mohammad.vali?source=post_page---byline--e4eb26691b14--------------------------------)[![Mohammad Hassan Vali](../Images/b057aa7bd9e1c629fc3743a7f69f013e.png)](https://medium.com/@mohammad.vali?source=post_page---byline--e4eb26691b14--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e4eb26691b14--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e4eb26691b14--------------------------------) [Mohammad Hassan Vali](https://medium.com/@mohammad.vali?source=post_page---byline--e4eb26691b14--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e4eb26691b14--------------------------------) ·9分钟阅读·2024年4月8日

--

> 本文简要介绍了我们新提出的无监督分布建模技术——填充空间的向量量化[1]，该方法在2023年Interspeech会议上发表。更多细节，请参阅[此链接](https://www.isca-archive.org/interspeech_2023/vali23_interspeech.pdf)中的论文。

![](../Images/6cc783b8a0d6df1f2d0381dab42c4cda.png)

图片来自[StockSnap.io](https://stocksnap.io)

深度生成模型是著名的基于神经网络的架构，它们学习一个潜在空间，该空间中的样本可以映射到合理的现实世界数据，如图像、视频和语音。这些潜在空间像一个黑盒，通常难以解释。在本文中，我们介绍了我们新提出的无监督分布建模技术，它结合了填充空间曲线和向量量化（VQ）两个概念，这种方法被称为**填充空间的向量量化**（SFVQ）[1]。SFVQ通过捕捉潜在空间的基本形态结构，帮助使潜在空间变得可解释。**需要特别注意的是，SFVQ是一个通用的分布建模工具，使用它并不局限于任何特定的神经网络架构，也不限于任何数据类型（例如图像、视频、语音等）**。在本文中，我们演示了如何应用SFVQ来解释语音转换模型的潜在空间。要理解本文，您不需要具备语音信号的技术知识，因为我们将以通俗的方式解释一切（非技术性）。在开始之前，让我先解释一下SFVQ技术及其工作原理。

## 空间填充向量量化（SFVQ）

[**向量量化**](https://medium.com/towards-data-science/improving-vector-quantization-in-vector-quantized-variational-autoencoders-vq-vae-915f5814b5ce) **(VQ)** [2] 是一种数据压缩技术，类似于 k-means 算法，能够对任意数据分布进行建模。下图展示了应用于高斯分布的 VQ。VQ 使用 32 个代码本向量（蓝色点）或簇对该分布（灰色点）进行聚类。每个 Voronoi 单元（绿色线）包含一个代码本向量，确保该代码本向量是位于该 Voronoi 单元内所有数据点的最近代码本向量（在欧几里得距离的意义上）。换句话说，每个代码本向量是其对应 Voronoi 单元中所有数据点的代表向量。因此，应用 VQ 于此高斯分布意味着将每个数据点映射到其最近的代码本向量，即用其最近的代码本向量表示每个数据点。有关 VQ 及其其他变体的更多信息，您可以查看[这篇文章](/optimizing-vector-quantization-methods-by-machine-learning-algorithms-77c436d0749d)。

![](../Images/81eaa7098328be722e18aa9bddb60c2c.png)

在使用 32 个代码本向量的高斯分布上应用向量量化。（图片由作者提供）

**空间填充曲线** 是一种通过递归规则生成的分段连续线，如果递归迭代无限重复，曲线会弯曲，直到完全填充一个多维空间。下图展示了希尔伯特曲线 [3]，这是一种著名的空间填充曲线，其角点在每次递归迭代中使用特定的数学公式定义。

![](../Images/b51dc7c74f0380333dd333794763186e.png)

填充 2D 正方形分布的希尔伯特曲线的前五次迭代。（图片由作者提供）

从空间填充曲线中获得直觉，我们可以将向量量化（VQ）视为将输入数据点映射到空间填充曲线（而不仅仅是将数据点映射到代码本向量，就像我们在普通 VQ 中所做的那样）。因此，我们将向量量化结合到空间填充曲线中，从而提出了我们的空间填充向量量化器（SFVQ）[1]，通过连续的分段线性曲线对 D 维数据分布进行建模，其角点是向量量化的代码本向量。下图展示了 VQ 和 SFVQ 在高斯分布上的应用。

![](../Images/b1c09ca46b6cb5e6418d27219253f3fb.png)

向量量化器的代码本向量（蓝色点）以及在高斯分布（灰色点）上应用的空间填充向量量化器（黑色曲线）。VQ 的 Voronoi 区域显示为绿色。（图片由作者提供）

关于如何训练 SFVQ 和如何将数据点映射到 SFVQ 曲线的技术细节，请参见[我们论文](https://www.isca-archive.org/interspeech_2023/vali23_interspeech.pdf) [1] 的第 2 节。

请注意，当我们在一个分布上训练一个普通的VQ时，学习到的代码簿矩阵中的相邻代码簿向量可能表示完全不同的内容。例如，第一个代码簿元素可能表示一个元音音位，而第二个可能表示语音信号中的静音部分。然而，当我们在一个分布上训练SFVQ时，学习到的代码簿向量将以一种有序的形式排列，使得代码簿矩阵中相邻的元素（即相邻的代码簿索引）会表示分布中相似的内容。我们可以利用SFVQ的这一特性来解释和探索深度神经网络（DNNs）中的潜在空间。作为一个典型的例子，下面我们将解释如何使用SFVQ方法来解释语音转换模型[4]的潜在空间。

## 语音转换

下图展示了一个基于向量量化变分自编码器（VQ-VAE）[5]架构的语音转换模型[4]。根据该模型，编码器将说话人A的语音信号作为输入，并将输出传递到向量量化（VQ）模块，从中提取语音信号中的语音信息（音位）。然后，这些语音信息与说话人B的身份一起输入解码器，解码器输出转换后的语音信号。转换后的语音将包含说话人A的语音信息（上下文）以及说话人B的身份。

![](../Images/b9b1c1e72ab0b9cc21295fb4fa7a726d.png)

基于VQ-VAE架构的语音转换模型。（图像由作者提供）

在该模型中，VQ模块充当信息瓶颈，学习语音的离散表示，仅捕获语音的语音内容并丢弃与说话人相关的信息。换句话说，VQ代码簿向量被期望只收集语音的音位相关内容。在这里，VQ输出的表示被视为该模型的潜在空间。我们的目标是用我们提出的SFVQ方法替代VQ模块，以解释潜在空间。通过解释，我们的意思是弄清楚每个潜在向量（代码簿向量）对应的音位是什么。

## 使用SFVQ解释潜在空间

我们评估了我们的空间填充向量量化器（SFVQ）在上面语音转换模型中寻找潜在空间（表示语音信息）结构的能力。为了进行评估，我们使用了TIMIT数据集[6]，因为它包含了使用[7]中音位集标记的数据。对于我们的实验，我们使用了以下音位分组：

+   **爆破音（塞音）：** {p, b, t, d, k, g, jh, ch}

+   **摩擦音：** {f, v, th, dh, s, z, sh, zh, hh, hv}

+   **鼻音：** {m, em, n, nx, ng, eng, en}

+   **元音：** {iy, ih, ix, eh, ae, aa, ao, ah, ax, ax-h, uh, uw, ux}

+   **半元音（近似音）：** {l, el, r, er, axr, w, y}

+   **双元音：** {ey, aw, ay, oy, ow}

+   **静音：** {h#}。

为了分析我们提出的SFVQ的性能，我们分别将标记过的TIMIT语音文件通过训练好的编码器和SFVQ模块，并提取对应于语音中所有音素的码本向量索引。换句话说，我们将带有标记音素的语音信号传入，并计算出已学习的SFVQ码本向量的索引，查看这些音素映射到哪个位置。如上所述，我们期望我们的SFVQ将相似的语音内容映射到彼此相邻的位置（在学习过的码本矩阵中的索引位置）。为了验证这一期望，以下图示我们展示了句子*“she had your dark suit”*的[谱图](https://en.wikipedia.org/wiki/Spectrogram)，以及它在普通向量量化器（VQ）和我们提出的SFVQ下对应的码本向量索引。

![](../Images/090c06ffb8078ca531dbac910b0bea67.png)

*(顶部) 使用我们提出的SFVQ（深蓝色圆圈）和普通VQ（灰色叉号）对语音信号的码本向量索引。 (底部) 包含与语音帧对应的码本向量索引的语音信号谱图。 (图片由作者提供)*

我们观察到，普通VQ的索引没有任何特定结构。然而，当使用我们提出的SFVQ时，码本向量索引的结构变得非常清晰。包含摩擦音{sh, s}的语音帧（出现在单词{she, suit}中）在整个帧中均匀分布在彼此相邻的位置。此外，包含音素{h#}的静音帧和一些其他低能量帧（如单词{dark, suit}中的{ kcl, tcl: k, t closures}）在0–20范围内均匀地排列在一起。请注意，以下图表对于具有相同语音内容的句子，在不同性别、语速和方言的说话者之间保持了一定的一致性。

下面的图展示了整个TIMIT语音文件中SFVQ的码本索引在每个语音组别（如上所述）上的直方图。乍一看，我们可以观察到辅音音素：{静音、爆破音、摩擦音、鼻音}和元音音素：{元音、双元音、近音}大约在索引125的位置被分开（除了索引20附近的峰值）。我们还注意到，不同组别的最显著峰值被分布在直方图的不同部分。

![](../Images/02c772787d2662cdd5edb31104c9ebd9.png)

*SFVQ的码本向量索引在不同语音组别下的直方图。（图片由作者提供）*

通过这种可视化，我们对潜在空间有了更好的理解，并且现在可以区分潜在空间中哪些部分对应于哪种语音组别。我们甚至可以进一步深入，按音素级别探索潜在空间。作为一个例子，以下图示为一个在摩擦音组中的所有音素分布的直方图。

![](../Images/54bdd8314ee51c54a2f70671b3d2f5ad.png)

*SFVQ代码本向量索引的直方图，针对摩擦音。（图片由作者提供）*

通过观察每个音素的最显著峰值，我们发现相似音素的峰值是相邻的。具体来说，我们在这里列出了相似音素及其对应的峰值索引，如{f:51, v:50}，{th:78, dh:80}，{s:71, z:67, sh:65, zh:67}，{hh:46, hv:50}。除了{hh, hv}音素外，摩擦音主要位于50–85的范围内。进一步的结构可以通过视觉检查从所有提供的图中轻松识别。

这些实验表明，我们提出的SFVQ能够实现对潜在代码本向量的连贯结构和易于解释的表示，这些向量表示输入语音的音素信息。因此，存在明显的音素分组区分，如{辅音与元音}，{摩擦音与鼻音与双元音与……}，并且我们可以简单地分辨出每个代码本向量所表示的音素。此外，特定音素组内的相似音素在潜在代码本空间中被编码在一起。这就是我们从一个被称为*潜在空间*的黑箱中希望获得的主要可解释性。

SFVQ相比其他试图使潜在空间可解释的监督方法有一个优势，那就是SFVQ不需要任何人工标签和对学习潜在空间的手动限制。为了使我们的方法可解释，它只要求用户通过观察完全研究一次无监督学习的潜在空间。这一观察所需的标记数据比监督训练大模型所需的数据少得多。**我们再次强调，SFVQ是一个通用工具，用于建模分布，使用它并不局限于任何特定的神经网络架构或任何数据类型（例如图像、视频、语音等）。**

# GitHub 仓库

我们的SFVQ技术的PyTorch实现已公开，在GitHub上可以通过以下链接访问：

[](https://github.com/MHVali/Space-Filling-Vector-Quantizer.git?source=post_page-----e4eb26691b14--------------------------------) [## GitHub - MHVali/Space-Filling-Vector-Quantizer

### 通过在GitHub上创建帐户，为MHVali/Space-Filling-Vector-Quantizer的发展做出贡献。

[github.com](https://github.com/MHVali/Space-Filling-Vector-Quantizer.git?source=post_page-----e4eb26691b14--------------------------------)

## 致谢

特别感谢我的博士导师[Prof. Tom Bäckström](https://research.aalto.fi/en/persons/tom-bäckström)，他在这项工作中给予了我支持，并且是这项工作的另一位贡献者。

# 参考文献

[1] M.H. Vali, T. Bäckström，《利用空间填充曲线进行语音转换中音素分析的可解释潜在空间》，发表于*Interspeech 会议论文集*，2023年。

[2] M. H. Vali 和 T. Bäckström，《NSVQ：用于机器学习的向量量化中的噪声替代》，*IEEE Access*，2022年。

[3] H. Sagan，《空间填充曲线*》，*Springer科学与商业媒体，2012年*。

[4] B. Van Niekerk, L. Nortje 和 H. Kamper, “用于音频单元发现的向量量化神经网络——Zerospeech 2020 挑战”，收录于*Interspeech 会议论文集*，2020年。

[5] A. Van Den Oord, O. Vinyals 和 K. Kavukcuoglu, “神经离散表示学习”，收录于*第31届国际神经信息处理系统会议论文集*，2017年。

[6] J. S. Garofolo, L. F. Lamel, W. M. Fisher, J. G. Fiscus, D. S. Pallett 和 N. L. Dahlgren, “*DARPA TIMIT 声学-语音连续语音语料库 CDROM*”，语言数据联盟，1993年。

[7] C. Lopes 和 F. Perdigao, “在 TIMIT 数据库上进行音素识别”，收录于*语音技术*。IntechOpen，2011年，第14章。[在线]。可用链接：[https://doi.org/10.5772/17600](https://doi.org/10.5772/17600)
