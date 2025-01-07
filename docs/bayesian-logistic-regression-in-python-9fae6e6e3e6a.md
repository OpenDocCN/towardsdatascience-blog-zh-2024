# Python 中的贝叶斯逻辑回归

> 原文：[`towardsdatascience.com/bayesian-logistic-regression-in-python-9fae6e6e3e6a?source=collection_archive---------4-----------------------#2024-02-20`](https://towardsdatascience.com/bayesian-logistic-regression-in-python-9fae6e6e3e6a?source=collection_archive---------4-----------------------#2024-02-20)

## 如何使用贝叶斯方法在 Python 中解决二分类问题。

[](https://medium.com/@fraserdbrown99?source=post_page---byline--9fae6e6e3e6a--------------------------------)![Fraser Brown](https://medium.com/@fraserdbrown99?source=post_page---byline--9fae6e6e3e6a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9fae6e6e3e6a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9fae6e6e3e6a--------------------------------) [Fraser Brown](https://medium.com/@fraserdbrown99?source=post_page---byline--9fae6e6e3e6a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9fae6e6e3e6a--------------------------------) ·8 分钟阅读·2024 年 2 月 20 日

--

![](img/c952b2a4d89662ea7f4b4ba17cbf1984.png)

贝叶斯思维 — OpenAI DALL-E 生成的图像，作者提供

## **介绍**

在本文中，我将使用 Pyro（一款 Python 概率编程包）构建一个简单的贝叶斯逻辑回归模型。本文将涵盖 EDA、特征工程、模型构建和评估。重点是提供一个简单的贝叶斯逻辑回归框架。因此，前两部分的深度将会有限。本文中使用的代码可以在此处找到：

[](https://github.com/fraser-brownn/bayesian_logistic_regression?source=post_page-----9fae6e6e3e6a--------------------------------) [## GitHub - fraser-brownn/bayesian_logistic_regression: 用于执行贝叶斯逻辑回归的 Notebook…

### 用于执行贝叶斯逻辑回归的 Notebook，使用的是心脏衰竭 Kaggle 数据集…

github.com](https://github.com/fraser-brownn/bayesian_logistic_regression?source=post_page-----9fae6e6e3e6a--------------------------------)

## **探索性数据分析**

我正在使用 Kaggle 上的心脏衰竭预测数据集，链接如下。此数据集是根据 Open Data Commons Open Database License (ODbL) v1.0 提供的。该数据集的完整引用可以在本文末尾找到。
