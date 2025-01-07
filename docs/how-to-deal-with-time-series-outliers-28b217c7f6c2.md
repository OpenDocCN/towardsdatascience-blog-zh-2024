# 如何处理时间序列中的异常值

> 原文：[`towardsdatascience.com/how-to-deal-with-time-series-outliers-28b217c7f6c2?source=collection_archive---------3-----------------------#2024-08-31`](https://towardsdatascience.com/how-to-deal-with-time-series-outliers-28b217c7f6c2?source=collection_archive---------3-----------------------#2024-08-31)

## 理解、检测和替换时间序列中的异常值

[](https://vcerq.medium.com/?source=post_page---byline--28b217c7f6c2--------------------------------)![Vitor Cerqueira](https://vcerq.medium.com/?source=post_page---byline--28b217c7f6c2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--28b217c7f6c2--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--28b217c7f6c2--------------------------------) [Vitor Cerqueira](https://vcerq.medium.com/?source=post_page---byline--28b217c7f6c2--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--28b217c7f6c2--------------------------------) ·阅读时长：6 分钟·2024 年 8 月 31 日

--

![](img/11ffe30adafc083a5ec656101fa38def.png)

图片由[Milton Villemar](https://unsplash.com/@villemar?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在这篇文章中，我们将探索：

+   时间序列异常值的不同类型

+   基于预测和基于估计的方法来检测异常值

+   如何通过替换处理不需要的异常值

# 异常值的类型

异常值是指那些与正常行为显著偏离的观测值。

时间序列可能会因为一些不寻常且不可重复的事件而出现异常值。这些异常值会影响时间序列分析，并使实践者得出错误的结论或产生不准确的预测。因此，识别和处理异常值是确保可靠时间序列建模的关键步骤。

在时间序列中，异常值通常分为两种类型：加性异常值和创新异常值。

## 加性异常值

加性异常值是指相较于历史数据，某个观测值显得异常高（或低）。

一个加性异常值的例子是由于促销或相关的病毒内容导致某个产品销量激增。有时这些异常值的产生是由于数据收集错误。加性异常值与...
