# 朝着 Mamba 状态空间模型迈进：图像、视频和时间序列

> 原文：[https://towardsdatascience.com/towards-mamba-state-space-models-for-images-videos-and-time-series-1e0bfdb5933a?source=collection_archive---------2-----------------------#2024-08-14](https://towardsdatascience.com/towards-mamba-state-space-models-for-images-videos-and-time-series-1e0bfdb5933a?source=collection_archive---------2-----------------------#2024-08-14)

## [🐍 朝着 Mamba 状态空间模型迈进：图像、视频和时间序列](https://towardsdatascience.com/tagged/mamba-image-video-signal)

## 第1部分

[](https://medium.com/@SaschaKirch?source=post_page---byline--1e0bfdb5933a--------------------------------)[![Sascha Kirch](../Images/a0d45da9dc9c602075b2810786c660c9.png)](https://medium.com/@SaschaKirch?source=post_page---byline--1e0bfdb5933a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1e0bfdb5933a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1e0bfdb5933a--------------------------------) [Sascha Kirch](https://medium.com/@SaschaKirch?source=post_page---byline--1e0bfdb5933a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1e0bfdb5933a--------------------------------) ·16分钟阅读·2024年8月14日

--

![](../Images/8e699ad3e8e2b18d79f099a7d89788ac.png)

图片由 [Sascha Kirch](https://medium.com/@SaschaKirch) 提供

> 这是我新系列的第一部分，[🐍 朝着 Mamba 状态空间模型迈进：图像、视频和时间序列](https://medium.com/@SaschaKirch/list/mamba-state-space-models-for-images-videos-and-timeseries-861ae0ad08fb)。

Mamba 是你所需的一切吗？当然，长期以来人们一直认为 A. Vaswani 等人在 2017 年提出的 [Attention is all you need](https://arxiv.org/abs/1706.03762) 中的 Transformer 架构就是如此。毫无疑问，Transformer 一次又一次地彻底改变了深度学习领域。它的通用架构可以轻松适应多种数据模式，如文本、图像、视频和时间序列，而且似乎你向 Transformer 投入的计算资源和数据越多，它的性能就越强。

然而，Transformer 的注意力机制有一个主要的缺点：它的复杂度是 *O(N²)*，这意味着它随着序列长度的增加而呈二次方增长。也就是说，输入序列越大，你需要的计算资源就越多，这使得处理大序列往往变得不可行。

❓ 问题是：我们能做得更好吗？是否有办法在保持性能的同时减少 *O(N²)* 复杂度？那么这种新架构会是什么样子的呢？
