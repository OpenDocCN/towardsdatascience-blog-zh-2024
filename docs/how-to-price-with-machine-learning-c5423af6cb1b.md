# 如何使用机器学习进行定价

> 原文：[https://towardsdatascience.com/how-to-price-with-machine-learning-c5423af6cb1b?source=collection_archive---------7-----------------------#2024-12-11](https://towardsdatascience.com/how-to-price-with-machine-learning-c5423af6cb1b?source=collection_archive---------7-----------------------#2024-12-11)

## 定制神经网络以适应价格响应函数

[](https://medium.com/@qshickkim?source=post_page---byline--c5423af6cb1b--------------------------------)[![Kyoosik Kim](../Images/94b74eb327b6c5d239484f2e4f4e1412.png)](https://medium.com/@qshickkim?source=post_page---byline--c5423af6cb1b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c5423af6cb1b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c5423af6cb1b--------------------------------) [Kyoosik Kim](https://medium.com/@qshickkim?source=post_page---byline--c5423af6cb1b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c5423af6cb1b--------------------------------) ·阅读时长6分钟·2024年12月11日

--

![](../Images/d19342fe1e1eb909fa94e0ed9ef84a70.png)

图片来自[Unsplash](https://unsplash.com/photos/two-male-and-female-mannequin-wearing-clothes-49mCO5ZRQDk)

无论我们销售商品还是服务，都必须为它们贴上价格标签。为了找到最优价格，我们需要了解顾客如何对价格做出反应。实现这一点的一种方式是使用价格响应函数。在本文中，我们将通过机器学习构建这个函数，并按照以下顺序利用它们来优化定价策略。

1.  定价基础：解释供需法则以及不同的价格响应函数

1.  价格响应函数与机器学习：使用神经网络模型构建价格响应函数

1.  定价优化：通过应用优化器到价格响应函数来找到最佳价格变化

我们将使用[这个过程](https://www.kaggle.com/code/qshick/ecommerce-synthetic-data)人工创建的示例数据集，该数据集模拟了典型的电子商务数据。

## 1\. 定价基础

我们都知道供需关系在定价中起着重要作用。需求大于供给时价格会上涨，反之亦然。我们称之为“供需法则”。下面是一个例子，描述价格与需求的关系。这是一个非常简单的“价格响应函数”。
