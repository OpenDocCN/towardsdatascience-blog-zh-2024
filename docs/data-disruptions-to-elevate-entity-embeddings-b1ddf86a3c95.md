# 数据破坏以提升实体嵌入

> 原文：[`towardsdatascience.com/data-disruptions-to-elevate-entity-embeddings-b1ddf86a3c95?source=collection_archive---------7-----------------------#2024-06-04`](https://towardsdatascience.com/data-disruptions-to-elevate-entity-embeddings-b1ddf86a3c95?source=collection_archive---------7-----------------------#2024-06-04)

## 在神经网络训练过程中注入随机值可以帮助你从类别特征中获得更多信息。

[](https://medium.com/@vla6?source=post_page---byline--b1ddf86a3c95--------------------------------)![Valerie Carey](https://medium.com/@vla6?source=post_page---byline--b1ddf86a3c95--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b1ddf86a3c95--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b1ddf86a3c95--------------------------------) [Valerie Carey](https://medium.com/@vla6?source=post_page---byline--b1ddf86a3c95--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b1ddf86a3c95--------------------------------) ·阅读时长 11 分钟·2024 年 6 月 4 日

--

![](img/65a2fea5cd298a5eeaefac2afe531fcc.png)

图片来源：[dylan nolte](https://unsplash.com/@dylan_nolte?utm_source=medium&utm_medium=referral) 于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

今天，我将讨论一种**随机正则化**方法，旨在提高神经网络模型中实体嵌入的泛化能力。我使用数据生成器在训练过程中随机注入选定的输入值，帮助模型学习如何处理未见过的代码。

**对于层次类别特征，性能提升尤其显著。** 随机化有助于模型利用更高级别的组信息来弥补未见过的低级别代码。

添加噪声、移除信息或以其他方式破坏数据，通常用于提高模型的鲁棒性并减少过拟合[1,2]。在这里，它被用来帮助模型学习如何处理缺失的类别信息。我将检查一个公共测试数据集，比较未修改数据与以两种方式进行随机化的数据。

> 当未见过的代码起作用时，随机注入值有助于模型进行泛化。

使用数据生成器随机打乱值，这样每个小批量都看到不同的场景，效果优于静态数据修改。

一个警告是，当编码层次结构与实际情况无关时，可能会发生过拟合...
