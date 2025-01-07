# MLOps — 使用 PyTest 进行数据验证

> 原文：[`towardsdatascience.com/mlops-data-validation-with-pytest-749641874871?source=collection_archive---------5-----------------------#2024-06-11`](https://towardsdatascience.com/mlops-data-validation-with-pytest-749641874871?source=collection_archive---------5-----------------------#2024-06-11)

![](img/2dd129918a5a651d3d0aeb7cc29e3ee8.png)

图片由[Michael Dziedzic](https://unsplash.com/@lazycreekimages?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 运行确定性和非确定性测试以验证你的数据集

[](https://medium.com/@marcellopoliti?source=post_page---byline--749641874871--------------------------------)![Marcello Politi](https://medium.com/@marcellopoliti?source=post_page---byline--749641874871--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--749641874871--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--749641874871--------------------------------) [Marcello Politi](https://medium.com/@marcellopoliti?source=post_page---byline--749641874871--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--749641874871--------------------------------) ·9 分钟阅读·2024 年 6 月 11 日

--

# 介绍

在 MLOps 管道中，我们尽量自动化尽可能多的步骤，目标是最小化程序员直接干预可能导致的错误数量，同时也要关注数据集验证。我相信大家都熟悉**机器学习的第 1 条规则：垃圾进，垃圾出**。无论我们开发的模型有多么复杂，如果数据集没有得到妥善处理，我们很有可能会得到糟糕的结果。

在本文中，我们将看到如何使用[PyTest](https://docs.pytest.org/en/8.2.x/)对数据集进行自动化验证。

我使用[Deepnote](https://deepnote.com/)运行本文中的脚本：这是一个基于云的笔记本，非常适合协作的数据科学项目和原型开发。

## 关于 ETL

初次接触机器学习的人通常需要解决一些像[Kaggle](https://www.kaggle.com/)上找到的挑战。在这些挑战中，我们几乎总是有一个静态数据集，它不会随时间变化。然而，在现实世界中，情况并非完全如此。

在处理实际的机器学习产品时，数据可能会不断变化。由此产生的...
