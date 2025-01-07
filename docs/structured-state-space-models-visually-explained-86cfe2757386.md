# 结构化状态空间模型视觉解析

> 原文：[`towardsdatascience.com/structured-state-space-models-visually-explained-86cfe2757386?source=collection_archive---------2-----------------------#2024-08-22`](https://towardsdatascience.com/structured-state-space-models-visually-explained-86cfe2757386?source=collection_archive---------2-----------------------#2024-08-22)

## [🐍 向 Mamba 状态空间模型迈进：图像、视频和时间序列](https://towardsdatascience.com/tagged/mamba-image-video-signal)

## 第二部分 — 向 Mamba 状态空间模型迈进：图像、视频和时间序列

[](https://medium.com/@SaschaKirch?source=post_page---byline--86cfe2757386--------------------------------)![Sascha Kirch](https://medium.com/@SaschaKirch?source=post_page---byline--86cfe2757386--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--86cfe2757386--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--86cfe2757386--------------------------------) [Sascha Kirch](https://medium.com/@SaschaKirch?source=post_page---byline--86cfe2757386--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--86cfe2757386--------------------------------) ·17 分钟阅读·2024 年 8 月 22 日

--

![](img/391d22003e4a683e4dae3c39f7251d9a.png)

图片来源：[Sascha Kirch](https://medium.com/@SaschaKirch).

> 这是我的新多部分系列的第二部分：[🐍 向 Mamba 状态空间模型迈进：图像、视频和时间序列](https://medium.com/@SaschaKirch/list/mamba-state-space-models-for-images-videos-and-timeseries-861ae0ad08fb).

状态空间模型，几十年来在许多工程学科中得到应用，现在也开始在深度学习中登场。在我们迈向 Mamba 选择性状态空间模型及其最新研究成果的过程中，理解状态空间模型至关重要。而且，正如在工程中常见的那样，正是这些细节让理论概念在实践中变得可行。除了状态空间模型之外，我们还必须讨论如何将它们应用于序列数据、如何处理长程依赖关系，以及如何通过利用某些矩阵结构高效地训练它们。

结构化状态空间模型为 Mamba 奠定了理论基础。然而，它们与系统理论和高级代数的关联可能是采用这一新框架的障碍之一。

所以，让我们一步一步解析，确保我们理解关键概念，并通过可视化帮助阐明这项新旧理论。
