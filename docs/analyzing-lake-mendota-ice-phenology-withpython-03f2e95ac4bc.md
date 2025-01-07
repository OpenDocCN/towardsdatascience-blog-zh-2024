# 使用Python分析门多塔湖冰冻现象

> 原文：[https://towardsdatascience.com/analyzing-lake-mendota-ice-phenology-withpython-03f2e95ac4bc?source=collection_archive---------7-----------------------#2024-06-10](https://towardsdatascience.com/analyzing-lake-mendota-ice-phenology-withpython-03f2e95ac4bc?source=collection_archive---------7-----------------------#2024-06-10)

## 一个可以立即使用的分析准备好的数据集

[](https://lukegloege.medium.com/?source=post_page---byline--03f2e95ac4bc--------------------------------)[![Luke Gloege博士](../Images/bad249704bb1a7278a046bfeedb952aa.png)](https://lukegloege.medium.com/?source=post_page---byline--03f2e95ac4bc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--03f2e95ac4bc--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--03f2e95ac4bc--------------------------------) [Luke Gloege博士](https://lukegloege.medium.com/?source=post_page---byline--03f2e95ac4bc--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--03f2e95ac4bc--------------------------------) ·9分钟阅读·2024年6月10日

--

![](../Images/2eb8aa036209f200c6b2d33fbf6e9c1a.png)

图片由 [Dave Hoefler](https://unsplash.com/@iamthedave?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

位于威斯康星州麦迪逊的门多塔湖被认为是世界上[最被研究的](https://integrativebiology.wisc.edu/2022/03/22/lake-mendota-said-to-be-most-studied-lake-in-the-world/)湖泊之一，拥有自19世纪50年代以来的冰冻现象记录。由威斯康星大学气候学办公室维护的这一记录，已经取得了令人瞩目的进展，使得数据变得可以[下载](https://climatology.nelson.wisc.edu/first-order-station-climate-data/madison-climate/lake-ice/history-of-ice-freezing-and-thawing-on-lake-mendota/)。然而，仅仅将冰面冻结日期随时间变化的简单图表仍然需要用户进行一些处理才能使其易于阅读。正因如此，我创建了[*mendotapy*](https://mendotapy.readthedocs.io/en/latest/)*，*一个开源（MIT许可）Python包，它允许以分析准备好的格式提取这些宝贵数据。它内置了一些工具，使得常见的转换操作变得轻松。

从气候角度来看，我觉得这个数据集非常有趣，但我认为任何人都能发现这个数据集的相关性。例如，它可以作为一个玩具数据集，用来练习回归分析、统计预测或谱分析。

在这篇文章中，我将描述这个包，展示一些数据的图表，并总结我在制作这个包时遇到的挑战。生成这些图形的所有代码都可以在本文中找到，包的代码可以在[GitHub](https://github.com/lgloege/mendotapy)上找到（欢迎贡献）。

# 安装和使用该包
