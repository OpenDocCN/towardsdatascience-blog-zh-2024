# 从MOCO v1到v3：构建自监督学习动态字典的探索 — 第一部分

> 原文：[https://towardsdatascience.com/from-moco-v1-to-v3-towards-building-a-dynamic-dictionary-for-self-supervised-learning-part-1-745dc3b4e861?source=collection_archive---------9-----------------------#2024-07-04](https://towardsdatascience.com/from-moco-v1-to-v3-towards-building-a-dynamic-dictionary-for-self-supervised-learning-part-1-745dc3b4e861?source=collection_archive---------9-----------------------#2024-07-04)

## 对动量对比学习框架的温和回顾

[](https://mengliuz.medium.com/?source=post_page---byline--745dc3b4e861--------------------------------)[![Mengliu Zhao](../Images/0b950a0785fa065db3319ed5be4a91de.png)](https://mengliuz.medium.com/?source=post_page---byline--745dc3b4e861--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--745dc3b4e861--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--745dc3b4e861--------------------------------) [孟刘赵](https://mengliuz.medium.com/?source=post_page---byline--745dc3b4e861--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--745dc3b4e861--------------------------------) ·7分钟阅读·2024年7月4日

--

**我们是否已经进入自监督学习的时代？**

数据每天都在流动。人们在全天候工作。工作分布到世界的每个角落。但仍然有大量数据未被注释，等待着可能通过新模型、新训练或新升级来使用。

或者，它将永远不会发生。当世界以监督方式运行时，它将永远不会发生。

近年来，自监督学习的兴起揭示了一个新的方向。自监督学习并不是为所有任务创建注释，而是将任务分为前置/预训练（请参见我之前关于预训练的文章[这里](https://medium.com/towards-data-science/from-masked-image-modeling-to-autoregressive-image-modeling-d9a3cadf72a1)）任务和下游任务。前置任务专注于从整个数据集中提取具有代表性的特征，而不依赖任何真实标签的指导。然而，这个任务仍然需要从数据集中自动生成标签，通常通过广泛的数据增强。因此，本文中我们将**无监督学习**（数据集未标注）和**自监督学习**（任务由自生成标签进行监督）交替使用。

**对比学习是自监督学习的一个主要类别**。它使用未标注的数据集和对比信息编码的损失（例如，对比损失、InfoNCE损失、三元组损失等）来训练深度学习网络。主要的对比学习方法包括SimCLR、SimSiam和MOCO系列。

MOCO——这个词是“动量对比”（momentum contrast）的缩写。其核心思想写在第一篇MOCO论文中，提出了对计算机视觉自监督学习问题的理解，如下所示：

*“[摘自* [*原始论文*](https://arxiv.org/pdf/1911.05722)*] 计算机视觉则进一步关注字典构建，因为原始信号位于一个连续的高维空间中，并且并非为了人类通信而构建……虽然有多种动机推动，这些（注：近期的视觉表示学习）方法可以被看作是在构建动态字典……* ***无监督学习训练编码器以执行字典查找：一个编码后的‘查询’应该与其匹配的键相似，并与其他键不同****。学习被表述为最小化对比损失。”*

在本文中，我们将温和地回顾MOCO v1到v3：

+   v1——论文《[用于无监督视觉表示学习的动量对比](http://openaccess.thecvf.com/content_CVPR_2020/html/He_Momentum_Contrast_for_Unsupervised_Visual_Representation_Learning_CVPR_2020_paper.html)》发表于CVPR 2020。该论文提出了对关键ResNet编码器进行动量更新，使用样本队列和InfoNCE损失。

+   v2——紧接着发布的论文《通过动量对比学习改进基线》实现了两个SimCLR架构的改进：a）用一个两层的MLP替换全连接层；b）通过加入模糊扩展原始数据增强。

+   v3——论文《自监督视觉变换器训练的实证研究》发表于ICCV 2021。该框架将关键查询对扩展为两个关键查询对，用于形成类似SimSiam的对称对比损失。骨干网络也从仅ResNet扩展为ResNet和ViT。

![](../Images/dda2a08a360af0f07bd3728dd432aa2c.png)

图像来源：[https://pxhere.com/en/photo/760197](https://pxhere.com/en/photo/760197)

**MOCO V1**

该框架从一个核心的自监督学习概念开始：**查询和键**。这里，查询指的是查询图像或图像块的表示向量（x^query），而键则指的是样本图像/图像块字典的表示向量（{x_0^key, x_1^key, …}）。查询向量q是由一个可训练的“主”编码器生成的，并通过常规的梯度反向传播进行训练。键向量则存储在字典队列中，由一个可训练的编码器生成，该编码器不会直接进行梯度反向传播，而是**仅通过使用主编码器的权重以动量方式更新权重**。下面是更新方式：

![](../Images/88f5658f2edadbd8df803bc6d4bc3be0.png)

图像来源：[https://arxiv.org/pdf/1911.05722](https://arxiv.org/pdf/1911.05722)

**实例判别任务与InfoNCE损失**

由于数据集在前期/预训练阶段没有标签，因此需要一个特定任务。论文采用了在[这篇CVPR 2018论文](https://arxiv.org/abs/1805.01978)中提出的实例区分任务。与原始设计不同，原始设计是使用非参数分类器计算内存库中特征向量之间的相似性，而MOCO论文使用了正向+<查询, 键>对和负向-<查询, 键>对来监督学习过程。当查询和键图像是从同一图像增强出来时，视为正样本。否则，视为负样本。训练损失是[InfoNCE损失](https://arxiv.org/pdf/1807.03748)，可以看作是查询/键对的softmax负对数：

![](../Images/3a804eee3aace41b596106edfe1ef9ed.png)

方程来源：[https://arxiv.org/pdf/1911.05722](https://arxiv.org/pdf/1911.05722)

**动量更新**

作者声称，将主查询编码器复制到键编码器可能导致较差的结果，因为快速变化的编码器会减少键表示字典的一致性。相反，每一步只训练主查询编码器，但键编码器的权重通过动量权重m进行更新：

![](../Images/0b9fd60b95b8d1c3aa764b82ab6ffd3d.png)

方程来源：[https://arxiv.org/pdf/1911.05722](https://arxiv.org/pdf/1911.05722)

在训练过程中，动量权重保持较大，例如0.999而不是0.9，这验证了作者的猜想，即关键编码器的一致性和稳定性会影响对比学习的表现。

**伪代码**

少于20行的伪代码是整个训练过程的快速概述。与上述的InfoLoss一致，值得注意的是，正对数值是每个样本的单一尺度，负对数值是每个样本对应K个键的K维向量。

![](../Images/dc25876e6c6a6be41a2118cf31b3f12b.png)

伪代码来源：[https://arxiv.org/pdf/1911.05722](https://arxiv.org/pdf/1911.05722)

**MOCO V2**

在MOCO发布后立即发布的2页v2论文，通过采纳SimCLR中的两项成功架构变更，对版本1进行了轻微修改：

+   将ResNet编码器的全连接层替换为2层MLP

+   扩展原始增强集，加入模糊增强

有趣的是，即使只有一个简单的架构调整，性能提升似乎也非常显著：

![](../Images/6d842ba1e192a83219ecafec8975afdf.png)

图像来源：[https://arxiv.org/pdf/2003.04297](https://arxiv.org/pdf/2003.04297)

**MOCO V3**

版本3通过采用对称对比损失、额外的投影头和ViT编码器，提出了重大改进。

**对称对比损失**

受到SimSiam工作的启发，该工作通过获取两个随机增强的视图，并在负余弦相似度计算中交换它们，从而获得对称损失，MOCO v3对样本进行了两次增强。它将样本分别输入查询编码器和键编码器。

![](../Images/1e16f89f803634cee1f310530d1272d9.png)

图像来源：[https://arxiv.org/pdf/1911.05722](https://arxiv.org/pdf/1911.05722) 和 [https://arxiv.org/abs/2104.02057](https://arxiv.org/abs/2104.02057)

对称对比损失基于一个简单假设——所有正样本对都位于 N*N 查询-关键矩阵的对角线位置，因为它们是同一图像的增强版本；所有负样本对则位于 N*N 查询-关键矩阵的其他位置，因为它们是来自不同样本的增强（可能是相同的增强）：

![](../Images/e7553907cd14dce1395aed58eb832258.png)

图像来源：[https://arxiv.org/abs/2104.02057](https://arxiv.org/abs/2104.02057)

从这个意义上讲，动态关键字典要简单得多，因为它是在小批量中动态计算的，不需要保持一个记忆队列。可以通过下面对批次大小的稳定性分析来验证这一点（注意，作者解释说 6144 批次的性能下降是由于 [训练期间的部分失败现象](https://arxiv.org/pdf/2104.02057)）：

![](../Images/3ef8e39e874c7e70617019ce85f83725.png)

图像来源：[https://arxiv.org/pdf/2104.02057](https://arxiv.org/pdf/2104.02057)

**ViT 编码器**

ViT 编码器带来的性能提升如下所示：

![](../Images/218a3243c511c15382c311f2927ab0d3.png)

图像来源：[https://arxiv.org/pdf/2104.02057](https://arxiv.org/pdf/2104.02057)

**比较与总结**

MOCO v3 论文给出了使用 ResNet50 (R50) 编码器的 v1-v3 性能比较：

![](../Images/2303db653ef6c32e0a45f98b02e24994.png)

图像来源：[https://arxiv.org/pdf/2104.02057](https://arxiv.org/pdf/2104.02057)

总结来说，MOCO v1-v3 对以下元素进行了明确的转变：

+   **编码器**：ResNet → ResNet + MLP 层 → ResNet/ViT + MLP 层

+   **关键字典**：全局关键向量队列 → 基于数据增强的小批量关键字

+   **对比损失**：非对称对比损失 → 对称对比损失

但这还不是全部。在下一篇文章中，我将深入探讨 [MOCO v3 代码](https://github.com/facebookresearch/moco-v3/tree/main)，以实现数据增强和动量更新。敬请期待！

**参考文献**

+   Wu 等人，通过非参数实例区分进行无监督特征学习。CVPR 2018。github：[https://github.com/zhirongw/lemniscate.pytorch](https://github.com/zhirongw/lemniscate.pytorch)

+   Oord 等人，对比预测编码的表示学习。arXiv 预印本 2018。

+   Chen 等人，视觉表示的对比学习简单框架。PMLR 2020。github：[https://github.com/sthalles/SimCLR](https://github.com/sthalles/SimCLR)

+   He 等人，动量对比用于无监督视觉表示学习。CVPR 2020。

+   Chen 等人，改进的基准通过动量对比学习。arXiv 预印本 2020。

+   陈等人，《探索简单的孪生表示学习》。CVPR 2021\. github: [https://github.com/facebookresearch/simsiam](https://github.com/facebookresearch/simsiam)

+   陈等人，《自监督视觉变换器训练的实证研究》。ICCV 2021\. github: [https://github.com/facebookresearch/moco-v3](https://github.com/facebookresearch/moco-v3)
