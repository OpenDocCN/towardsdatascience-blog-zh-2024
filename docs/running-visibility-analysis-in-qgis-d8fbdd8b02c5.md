# 在QGIS中进行可视性分析

> 原文：[https://towardsdatascience.com/running-visibility-analysis-in-qgis-d8fbdd8b02c5?source=collection_archive---------6-----------------------#2024-10-04](https://towardsdatascience.com/running-visibility-analysis-in-qgis-d8fbdd8b02c5?source=collection_archive---------6-----------------------#2024-10-04)

## 学习如何使用免费的GIS软件和数据轻松进行可视性分析，并利用结果创建令人惊叹的可视化效果

[](https://medium.com/@helenmakesmaps?source=post_page---byline--d8fbdd8b02c5--------------------------------)[![Helen McKenzie](../Images/68b33e3dee55611b60b365fe08658430.png)](https://medium.com/@helenmakesmaps?source=post_page---byline--d8fbdd8b02c5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d8fbdd8b02c5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d8fbdd8b02c5--------------------------------) [Helen McKenzie](https://medium.com/@helenmakesmaps?source=post_page---byline--d8fbdd8b02c5--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d8fbdd8b02c5--------------------------------) ·阅读时间9分钟·2024年10月4日

--

我最喜欢的空间分析类型之一就是可视性分析。它是一个非常简单的概念，允许你理论上计算出某个物体从哪里可以被看到。可视性分析主要有两种形式——也叫视域分析或ZTVs（理论可视区）。这两种形式分别是：

+   “标准”视域分析：我能从这个位置看到什么？例如，如果我站在一座山顶上，我的视野将是什么样的？

+   “反向”视域分析：我能看到哪些位置？例如，如果我站在一座山顶上，谁能看到我？

它的工作原理是通过取一组观察者点和一个地形模型，然后计算该地形模型的每个部分与观察者点之间的视线。听起来这会是一个很慢的过程，对吧？确实可以是。不过，结果非常惊人，并且对某些应用来说是不可或缺的。

最常见的一个应用是帮助人们了解一个新开发项目——比如风力发电场、太阳能电池板站点或高速公路——从哪些地方能看到。分析人员可以基于他们的发现建模不同的场景，例如如果他们在**这个**精确的位置种上一排树，视野会发生什么变化？可视性分析还可以用于推动广告牌的定价策略，或者帮助…
