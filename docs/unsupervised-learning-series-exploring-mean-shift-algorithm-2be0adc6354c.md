# 无监督学习系列：探索均值漂移算法

> 原文：[`towardsdatascience.com/unsupervised-learning-series-exploring-mean-shift-algorithm-2be0adc6354c?source=collection_archive---------2-----------------------#2024-08-19`](https://towardsdatascience.com/unsupervised-learning-series-exploring-mean-shift-algorithm-2be0adc6354c?source=collection_archive---------2-----------------------#2024-08-19)

## 让我们学习一下最著名的基于密度的聚类算法之一——均值漂移。

[](https://ivopbernardo.medium.com/?source=post_page---byline--2be0adc6354c--------------------------------)![Ivo Bernardo](https://ivopbernardo.medium.com/?source=post_page---byline--2be0adc6354c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2be0adc6354c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2be0adc6354c--------------------------------) [Ivo Bernardo](https://ivopbernardo.medium.com/?source=post_page---byline--2be0adc6354c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2be0adc6354c--------------------------------) ·10 分钟阅读·2024 年 8 月 19 日

--

![](img/a7e8282c5ca208b6beebfc2ab32ab5df.png)

*K 均值、层次聚类、期望最大化* 和 *DBScan* 可能是你在机器学习领域最为熟悉的几种聚类算法。

然而，还有另一个基于密度的算法，它已经解决了各种问题，从卫星图像分割到物体追踪，它的名字是均值漂移。这个算法因通过在数据点密度较高的区域周围构建聚类来寻找特定数据集的模态而闻名。

可视化均值漂移是很简单的。通常，我会使用摩天大楼天际线的类比。如果你考虑摩天大楼天际线：

![](img/b698b2c4cabf19558bfe21a71ac29606.png)

使用摩天大楼的密度簇 — 由 AI 在 Microsoft Image Designer 中生成的图像

如果我们将摩天大楼视为我们的数据点，那么我们可以清楚地看到两种不同的密度簇：

![](img/2fea888fb217ede6443e0f089a8cbd95.png)

使用摩天大楼的密度簇 — 由 AI 在 Microsoft Image Designer 中生成的图像
