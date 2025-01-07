# 如何构建用于节点分类的神经网络

> 原文：[`towardsdatascience.com/how-to-build-neural-networks-for-node-classification-from-tabular-data-bdd07ceaef83?source=collection_archive---------6-----------------------#2024-05-13`](https://towardsdatascience.com/how-to-build-neural-networks-for-node-classification-from-tabular-data-bdd07ceaef83?source=collection_archive---------6-----------------------#2024-05-13)

## 学习如何在 13 分钟内使用 PyTorch Geometric 从 CSV 文件构建基于图的神经网络

[](https://ds-claudia.medium.com/?source=post_page---byline--bdd07ceaef83--------------------------------)![Claudia Ng](https://ds-claudia.medium.com/?source=post_page---byline--bdd07ceaef83--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bdd07ceaef83--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bdd07ceaef83--------------------------------) [Claudia Ng](https://ds-claudia.medium.com/?source=post_page---byline--bdd07ceaef83--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bdd07ceaef83--------------------------------) ·13 分钟阅读·2024 年 5 月 13 日

--

# 介绍

许多数据类型本质上是关系型的，基于图的神经网络是强大的模型，可以学习网络中的复杂模式。

在本文中，我们将介绍如何构建 PyTorch `Data`对象以处理同质图数据，然后训练不同类型的神经网络来预测一个节点属于哪个类别。这种预测问题通常被称为节点分类。

我们将使用来自《多尺度属性节点嵌入》论文中的 Facebook 大型页面-页面网络¹ [数据集](https://snap.stanford.edu/data/facebook-large-page-page-network.html)，该论文由 Benedek Rozemberczki、Carl Allen 和 Rik Sarkar 于 2019 年发表在《复杂网络期刊》上。

该数据集包含 22,470 个 Facebook 页面，这些页面根据主题被分类为四个类别之一。每个出版物通过一个大小不一的特征向量来表示。数据集还包含有关 Facebook 页面的跟随信息，总共有 171,992 个链接或边缘。

本文详细介绍了从表格数据构建基于图的神经网络的五个步骤：

1) 从 URL 下载数据，

2) 从表格数据创建 PyTorch `Data`对象，
