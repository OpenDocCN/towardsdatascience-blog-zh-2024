# 使用 sktime 进行高级时间序列预测

> 原文：[`towardsdatascience.com/advanced-time-series-forecasting-with-sktime-af8eabc76173?source=collection_archive---------2-----------------------#2024-11-11`](https://towardsdatascience.com/advanced-time-series-forecasting-with-sktime-af8eabc76173?source=collection_archive---------2-----------------------#2024-11-11)

## 学习如何在几行代码中优化模型超参数，甚至进行架构优化

[](https://dr-robert-kuebler.medium.com/?source=post_page---byline--af8eabc76173--------------------------------)![Dr. Robert Kübler](https://dr-robert-kuebler.medium.com/?source=post_page---byline--af8eabc76173--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--af8eabc76173--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--af8eabc76173--------------------------------) [Dr. Robert Kübler](https://dr-robert-kuebler.medium.com/?source=post_page---byline--af8eabc76173--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--af8eabc76173--------------------------------) ·9 分钟阅读·2024 年 11 月 11 日

--

![](img/fea3cddb258c8b82b0f895177e65b7b2.png)

图片来自[Johnny](https://unsplash.com/@johnny69px?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在我之前的文章中，我们探讨了使用 sktime 进行时间序列预测的基础知识，学习了如何利用这个强大的库进行简单的预测任务。现在，是时候将我们的旅程推进，并深入了解一些高级技巧，帮助你优化预测结果并提高准确性。在这篇后续文章中，我们将探索如何构建更复杂的模型，调整超参数，甚至使用 sktime 进行模型架构搜索。

[](/convenient-time-series-forecasting-with-sktime-bb82375e846c?source=post_page-----af8eabc76173--------------------------------) ## 使用 sktime 进行便捷的时间序列预测

### 如何让预测变得像散步一样简单

towardsdatascience.com

# 回顾

首先，为了便于入门，让我再次演示基本的 sktime 工作流程。这一次，我们将使用**Longley 数据集**，它是[sktime 的一部分](https://www.sktime.net/en/stable/api_reference/auto_generated/sktime.datasets.load_longley.html)（BSD-3 许可证）。该数据集包含了从 1947 年到 1962 年间的各种美国宏观经济变量，内容如下：
