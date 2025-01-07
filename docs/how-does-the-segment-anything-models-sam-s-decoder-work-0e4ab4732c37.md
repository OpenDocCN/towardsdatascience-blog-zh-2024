# Segment-Anything 模型（SAM）的解码器是如何工作的？

> 原文：[`towardsdatascience.com/how-does-the-segment-anything-models-sam-s-decoder-work-0e4ab4732c37?source=collection_archive---------5-----------------------#2024-03-24`](https://towardsdatascience.com/how-does-the-segment-anything-models-sam-s-decoder-work-0e4ab4732c37?source=collection_archive---------5-----------------------#2024-03-24)

[](https://jasonweiyi.medium.com/?source=post_page---byline--0e4ab4732c37--------------------------------)![Wei Yi](https://jasonweiyi.medium.com/?source=post_page---byline--0e4ab4732c37--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0e4ab4732c37--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0e4ab4732c37--------------------------------) [Wei Yi](https://jasonweiyi.medium.com/?source=post_page---byline--0e4ab4732c37--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0e4ab4732c37--------------------------------) ·18 分钟阅读·2024 年 3 月 24 日

--

![](img/b352f48ee952208b5f82ab47be181458.png)

图片来自 [dylan nolte](https://unsplash.com/@dylan_nolte?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

深入探讨 Segment-Anything 模型的解码过程，重点讲解其自注意力和交叉注意力机制是如何工作的。

本文只关注 SAM 的解码器。如果你对 SAM 的编码器感兴趣，请参考我的另一篇文章 “[Segment-Anything 模型（SAM）的编码器是如何工作的？](https://medium.com/towards-data-science/how-does-the-segment-anything-models-sam-s-encoder-work-003a8a6e3f8b)”。

Segment-Anything (SAM) 模型是一个 2D 交互式分割模型，或者说是引导模型。SAM 需要用户提示来分割图像。这些提示告诉模型应该在哪个位置进行分割。模型的输出是不同层级的分割掩码集合，并且每个掩码都有一个对应的置信度分数。

分割掩码是一个与输入图像大小相同的 2D 二进制数组。在这个 2D 数组中，位于 *(x, y)* 位置的条目如果模型认为该像素属于分割区域，则其值为 1，否则为 0。那些置信度分数表示模型对每个分割质量的信任程度，分数越高，质量越高。

SAM 的网络架构由编码器和解码器组成：

+   编码器接收图像和用户提示输入，生成图像嵌入、图像位置嵌入和用户提示嵌入。

+   解码器接收……
