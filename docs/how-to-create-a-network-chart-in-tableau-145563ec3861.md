# 如何在 Tableau 中创建网络图

> 原文：[`towardsdatascience.com/how-to-create-a-network-chart-in-tableau-145563ec3861?source=collection_archive---------12-----------------------#2024-11-05`](https://towardsdatascience.com/how-to-create-a-network-chart-in-tableau-145563ec3861?source=collection_archive---------12-----------------------#2024-11-05)

## 《在 Tableau 中创建网络图的逐步指南》

[](https://medium.com/@robinvm?source=post_page---byline--145563ec3861--------------------------------)![Robin von Malottki](https://medium.com/@robinvm?source=post_page---byline--145563ec3861--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--145563ec3861--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--145563ec3861--------------------------------) [Robin von Malottki](https://medium.com/@robinvm?source=post_page---byline--145563ec3861--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--145563ec3861--------------------------------) ·阅读时间 8 分钟·2024 年 11 月 5 日

--

![](img/c05d7a5a3deceb5f49fbc79420099c9b.png)

图片由[Radoslaw Sikorski](https://www.pexels.com/de-de/foto/luftaufnahme-von-brucken-und-gleisen-im-herbst-29097273/)提供

并由 Pexels.com 提供

在一个越来越依赖互联数据的世界里，理解不同实体之间的关系比以往任何时候都更为重要。网络图，也叫网络图谱或关系图，提供了一种引人注目的方式来可视化这些连接，为复杂的数据集提供了清晰度和洞察力。无论你是在分析社交网络、组织结构、沟通流程，还是任何由互相关联的元素组成的系统，网络图都能帮助你看到更广阔的图景。

在这篇博客中，我将带你走一遍使用 Tableau 创建网络图的过程，特别关注组织内经理与员工之间的关系结构。了解员工与经理之间的连接对于可视化组织层级、增强团队协作和识别潜在的沟通差距至关重要。

**网络图的关键组成部分**

在深入了解网络图的创建之前，首先需要理解网络可视化的架构。让我们以一个小公司的组织结构图为例，来展示网络图的形式。
