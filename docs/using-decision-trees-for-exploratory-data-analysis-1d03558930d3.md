# 使用决策树进行探索性数据分析

> 原文：[https://towardsdatascience.com/using-decision-trees-for-exploratory-data-analysis-1d03558930d3?source=collection_archive---------1-----------------------#2024-06-20](https://towardsdatascience.com/using-decision-trees-for-exploratory-data-analysis-1d03558930d3?source=collection_archive---------1-----------------------#2024-06-20)

## 将决策树添加到您的EDA中，并从一开始就获得极好的见解

[](https://gustavorsantos.medium.com/?source=post_page---byline--1d03558930d3--------------------------------)[![Gustavo R Santos](../Images/a19a9f4525cdeb6e7a76cd05246aa622.png)](https://gustavorsantos.medium.com/?source=post_page---byline--1d03558930d3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1d03558930d3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1d03558930d3--------------------------------) [Gustavo R Santos](https://gustavorsantos.medium.com/?source=post_page---byline--1d03558930d3--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1d03558930d3--------------------------------) ·阅读时间7分钟·2024年6月20日

--

![](../Images/9b673ec881933c343e757a6924c27d99.png)

图片由 [Mila Tovar](https://unsplash.com/@milatovar?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来源：[Unsplash](https://unsplash.com/photos/black-bare-tree-under-white-sky-during-daytime-Ku-1SYS0o7k?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

# 引言

*决策树（DT）* 是最直观的机器学习算法。

我说出来了。这只是我的观点。但我相信这也是数据科学领域中一种常见的感受。

> 决策树（…）机器学习方法，也通过一系列简单选择做出复杂决策。[Brett Lantz，《用R进行机器学习》]

决策树在运筹学和数据科学领域得到广泛应用，其成功的原因在于它遵循了与人类决策过程相似的过程。这个过程基于一个流程图，每个节点将对给定变量做出一个简单的二元决策，直到我们做出最终决定。

一个简单的例子：购买T恤。如果我想买一件衬衫，我可能会考虑一些变量，比如价格、品牌、尺寸和颜色。所以我从预算开始我的决策过程：

+   如果价格超过30美元，我就不买。否则，我会买。

+   一旦我找到了价格低于30美元的商品，我希望它来自...
