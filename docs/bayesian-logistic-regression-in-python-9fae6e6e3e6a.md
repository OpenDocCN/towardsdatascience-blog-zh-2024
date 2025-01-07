# Python中的贝叶斯逻辑回归

> 原文：[https://towardsdatascience.com/bayesian-logistic-regression-in-python-9fae6e6e3e6a?source=collection_archive---------4-----------------------#2024-02-20](https://towardsdatascience.com/bayesian-logistic-regression-in-python-9fae6e6e3e6a?source=collection_archive---------4-----------------------#2024-02-20)

## 如何使用贝叶斯方法在Python中解决二分类问题。

[](https://medium.com/@fraserdbrown99?source=post_page---byline--9fae6e6e3e6a--------------------------------)[![Fraser Brown](../Images/d5f99897c571a61ed8832e5542751973.png)](https://medium.com/@fraserdbrown99?source=post_page---byline--9fae6e6e3e6a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9fae6e6e3e6a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9fae6e6e3e6a--------------------------------) [Fraser Brown](https://medium.com/@fraserdbrown99?source=post_page---byline--9fae6e6e3e6a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9fae6e6e3e6a--------------------------------) ·8分钟阅读·2024年2月20日

--

![](../Images/c952b2a4d89662ea7f4b4ba17cbf1984.png)

贝叶斯思维 — OpenAI DALL-E 生成的图像，作者提供

## **介绍**

在本文中，我将使用Pyro（一款Python概率编程包）构建一个简单的贝叶斯逻辑回归模型。本文将涵盖EDA、特征工程、模型构建和评估。重点是提供一个简单的贝叶斯逻辑回归框架。因此，前两部分的深度将会有限。本文中使用的代码可以在此处找到：

[](https://github.com/fraser-brownn/bayesian_logistic_regression?source=post_page-----9fae6e6e3e6a--------------------------------) [## GitHub - fraser-brownn/bayesian_logistic_regression: 用于执行贝叶斯逻辑回归的Notebook…

### 用于执行贝叶斯逻辑回归的Notebook，使用的是心脏衰竭Kaggle数据集…

github.com](https://github.com/fraser-brownn/bayesian_logistic_regression?source=post_page-----9fae6e6e3e6a--------------------------------)

## **探索性数据分析**

我正在使用Kaggle上的心脏衰竭预测数据集，链接如下。此数据集是根据Open Data Commons Open Database License (ODbL) v1.0提供的。该数据集的完整引用可以在本文末尾找到。
