# 理解时间序列的结构性变化

> 原文：[`towardsdatascience.com/understanding-time-series-structural-changes-f6a4c44cb13c?source=collection_archive---------4-----------------------#2024-09-04`](https://towardsdatascience.com/understanding-time-series-structural-changes-f6a4c44cb13c?source=collection_archive---------4-----------------------#2024-09-04)

## 如何使用 Python 检测时间序列变化点

[](https://vcerq.medium.com/?source=post_page---byline--f6a4c44cb13c--------------------------------)![Vitor Cerqueira](https://vcerq.medium.com/?source=post_page---byline--f6a4c44cb13c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f6a4c44cb13c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f6a4c44cb13c--------------------------------) [Vitor Cerqueira](https://vcerq.medium.com/?source=post_page---byline--f6a4c44cb13c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f6a4c44cb13c--------------------------------) ·7 分钟阅读·2024 年 9 月 4 日

--

![](img/709cd6e791407b03f1de3f62f08fb787.png)

摄影： [Robby McCullough](https://unsplash.com/@mybbor?utm_source=medium&utm_medium=referral) 来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在本文中，我们将：

+   定义时间序列的结构性变化是什么，以及它们与异常值的区别。

+   概述不同类型的结构性变化。

+   探索变化点检测方法，例如使用 kats 和 ruptures 包的 CUSUM 方法。

# 引言

平稳性是时间序列分析和预测中的核心概念。在平稳性条件下，时间序列的属性（如均值）保持不变，仅有一些偶然的波动。

然而，现实世界的数据集中很少观察到平稳性。时间序列可能会出现结构性变化或断裂，这些变化引入了非平稳性变异，改变了时间序列的分布。标志变化开始的时间步称为变化点。

检测结构性变化在时间序列分析和预测中非常有价值。新出现的分布往往使得过去的数据变得过时，进而影响其中的模型。这就需要你通过最近的数据或其他合适的策略来更新你的模型。如果历史数据中出现变化点，你可以通过特征工程来处理它们。
