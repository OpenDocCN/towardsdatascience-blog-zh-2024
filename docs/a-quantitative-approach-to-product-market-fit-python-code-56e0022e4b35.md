# 定量方法在产品市场契合度中的应用（Python 代码）

> 原文：[`towardsdatascience.com/a-quantitative-approach-to-product-market-fit-python-code-56e0022e4b35?source=collection_archive---------4-----------------------#2024-04-08`](https://towardsdatascience.com/a-quantitative-approach-to-product-market-fit-python-code-56e0022e4b35?source=collection_archive---------4-----------------------#2024-04-08)

## 使用 Python 在你的用户数据中寻找意义

[](https://medium.com/@myrthings?source=post_page---byline--56e0022e4b35--------------------------------)![Myriam Barnés](https://medium.com/@myrthings?source=post_page---byline--56e0022e4b35--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--56e0022e4b35--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--56e0022e4b35--------------------------------) [Myriam Barnés](https://medium.com/@myrthings?source=post_page---byline--56e0022e4b35--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--56e0022e4b35--------------------------------) ·12 分钟阅读·2024 年 4 月 8 日

--

![](img/ca698b024efd645090a245b7929dfb7a.png)

图片来源：[Jason Coudriet](https://unsplash.com/@jcoudriet?utm_source=medium&utm_medium=referral)来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

曾经，我在一家种子风险投资公司从事数据工作。我的部分工作内容是与创业者讨论他们商业中的**产品市场契合度指标**。我通常会使用[Andrew Chen 的定量方法](https://tribecap.co/a-quantitative-approach-to-product-market-fit/)来分析产品市场契合度。我们首先与创业者讨论产品市场契合度的指标，然后请他们提供用户的原始数据。接着，我会处理这些数据，以寻找不同用户及其行为随时间变化的趋势。

在这种定量方法中，主要有**两种表示方式**：*增长核算*和*用户群分析*。在初创公司中，原始数据通常很混乱，因此我需要一遍又一遍地编写代码来调整数据。然而，几周前，我翻开了我的旧笔记本。随着时间和视角的变化，我决定**将框架进行概括**，并创建更易使用的 Python 对象。这样，你只需要清理和准备数据集，数据可视化部分已经完成！

我编写了**两个对象：一个用于*增长核算*，另一个用于*用户群分析***。它们是以*机器学习风格*构建的，因此你可以设置参数，用你的数据拟合对象，然后请求结果和数据可视化。*分布情况*……
