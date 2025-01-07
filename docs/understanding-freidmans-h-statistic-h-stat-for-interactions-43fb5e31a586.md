# 理解费曼的 H 统计量（H-stat）用于交互作用分析

> 原文：[`towardsdatascience.com/understanding-freidmans-h-statistic-h-stat-for-interactions-43fb5e31a586?source=collection_archive---------9-----------------------#2024-05-28`](https://towardsdatascience.com/understanding-freidmans-h-statistic-h-stat-for-interactions-43fb5e31a586?source=collection_archive---------9-----------------------#2024-05-28)

## 这篇文章介绍了 H 统计量的直觉和数学原理，H-stat 用于在机器学习模型中寻找交互作用

[](https://conorosullyds.medium.com/?source=post_page---byline--43fb5e31a586--------------------------------)![Conor O'Sullivan](https://conorosullyds.medium.com/?source=post_page---byline--43fb5e31a586--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--43fb5e31a586--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--43fb5e31a586--------------------------------) [Conor O'Sullivan](https://conorosullyds.medium.com/?source=post_page---byline--43fb5e31a586--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--43fb5e31a586--------------------------------) ·阅读时间 11 分钟·2024 年 5 月 28 日

--

![](img/bd982f8445158a4a5d575575b01f3446.png)

（来源：作者）

吸入石棉会增加吸烟者比非吸烟者患肺癌的风险。药物的副作用可能依赖于性别。你的车随着年龄的增长会贬值——除非它是一辆经典车。这些都是交互作用的例子。识别并结合这些交互作用可以大大提高模型的准确性并改变模型的解释。那么问题是，我们如何找到这些交互作用呢？

一种方法是应用可解释人工智能方法来训练一个模型，针对你认为可能存在交互作用的特征。我们将探讨其中的一种方法——费曼的 H 统计量。这也被称为 H-stat 或 H 指数。具体来说，我们将：

+   在[PDPs 和 ICE 图](https://medium.com/towards-data-science/the-ultimate-guide-to-pdps-and-ice-plots-4182885662aa)的帮助下，阐明该方法的直觉原理。

+   深入解释其数学原理。

+   了解其局限性及如何克服这些局限性。

我们将看到该统计量有两个版本。一个用于**两个特征之间的交互作用**，另一个用于一个特征与**所有其他特征**之间的交互作用。在探索其局限性时，我们还将看到存在一个**非标准化版本**的...
