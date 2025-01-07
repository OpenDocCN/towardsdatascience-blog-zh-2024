# Mamba来了：选择性状态空间模型

> 原文：[https://towardsdatascience.com/here-comes-mamba-the-selective-state-space-model-435e5d17a451?source=collection_archive---------4-----------------------#2024-09-03](https://towardsdatascience.com/here-comes-mamba-the-selective-state-space-model-435e5d17a451?source=collection_archive---------4-----------------------#2024-09-03)

## [🐍 向Mamba状态空间模型迈进：适用于图像、视频和时间序列](https://towardsdatascience.com/tagged/mamba-image-video-signal)

## 第3部分 — 向Mamba状态空间模型迈进：适用于图像、视频和时间序列

[](https://medium.com/@SaschaKirch?source=post_page---byline--435e5d17a451--------------------------------)[![Sascha Kirch](../Images/a0d45da9dc9c602075b2810786c660c9.png)](https://medium.com/@SaschaKirch?source=post_page---byline--435e5d17a451--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--435e5d17a451--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--435e5d17a451--------------------------------) [Sascha Kirch](https://medium.com/@SaschaKirch?source=post_page---byline--435e5d17a451--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--435e5d17a451--------------------------------) ·阅读时间17分钟·2024年9月3日

--

![](../Images/809aa25fa36bff0e6df683db98e295c2.png)

图片来源：[Sascha Kirch](https://medium.com/@SaschaKirch).

> 这是我新系列的第3部分：[🐍 向Mamba状态空间模型迈进：适用于图像、视频和时间序列](https://medium.com/@SaschaKirch/list/mamba-state-space-models-for-images-videos-and-timeseries-861ae0ad08fb)。

Mamba，这个被认为将取代强大的Transformer的模型，从最初在深度学习中使用状态空间模型（SSMs）的构想到现在，已经走过了很长的路。

Mamba为状态空间模型添加了选择性，从而在保持SSM子二次工作复杂度的同时，实现了类似Transformer的性能。其高效的选择性扫描比标准实现快40倍，并且在吞吐量上可达到Transformer的5倍。

加入我，一同深入探讨Mamba，我们将发现选择性如何解决以往状态空间模型（SSMs）的局限性，Mamba如何克服这些变化带来的新挑战，以及我们如何将Mamba融入现代深度学习架构中。
