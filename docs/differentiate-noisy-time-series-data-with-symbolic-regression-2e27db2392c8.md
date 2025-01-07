# 使用符号回归区分嘈杂的时间序列数据

> 原文：[`towardsdatascience.com/differentiate-noisy-time-series-data-with-symbolic-regression-2e27db2392c8?source=collection_archive---------5-----------------------#2024-09-13`](https://towardsdatascience.com/differentiate-noisy-time-series-data-with-symbolic-regression-2e27db2392c8?source=collection_archive---------5-----------------------#2024-09-13)

## **一个逐步示例，展示如何在数据稀缺的情况下推导出嘈杂的时间序列轮廓**

[](https://medium.com/@mit.forster?source=post_page---byline--2e27db2392c8--------------------------------)![Tim Forster](https://medium.com/@mit.forster?source=post_page---byline--2e27db2392c8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2e27db2392c8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2e27db2392c8--------------------------------) [Tim Forster](https://medium.com/@mit.forster?source=post_page---byline--2e27db2392c8--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2e27db2392c8--------------------------------) ·14 分钟阅读 ·2024 年 9 月 13 日

--

![](img/3b79e03bb57499d6f4d4b1fc4a407c58.png)

图片来源：Jake Hills，来自 Unsplash

> 注意：如果您没有 Medium 订阅，可以在 这里 免费阅读本文！

时间序列轮廓在我们的日常生活中无处不在。也有许多专门的研究工作在探讨这一主题。

简单来说，时间序列轮廓是一个后续数据点的集合 *y(0), y(1), … ,y(t)*，其中时刻 *t* 的一个数据点依赖于时刻 *t-1* （甚至更早时间）的数据点。

在许多应用中，人们希望预测在某些先前数据点已知的情况下，时间序列轮廓如何变化。为了实现这一点，存在各种建模方法。它们的核心可能是获取一些关于过去（或现在）的信息，然后估算未来轮廓的样子。可以找到许多涉及时间序列预测的研究工作，例如使用神经网络描述天气变化（[Bi 等人，2023](https://www.nature.com/articles/s41586-023-06185-3)），通过深度学习预测股票价格行为（[Xiao 和 Su，2022](https://onlinelibrary.wiley.com/doi/10.1155/2022/4758698)），或预测药品的需求变化（[Rathipriya 等人，2023](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9540101/)）。当然，这些研究工作是我通过快速搜索找到的，所以…
