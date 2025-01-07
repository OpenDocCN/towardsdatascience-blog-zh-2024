# 如何构建用于节点分类的神经网络

> 原文：[https://towardsdatascience.com/how-to-build-neural-networks-for-node-classification-from-tabular-data-bdd07ceaef83?source=collection_archive---------6-----------------------#2024-05-13](https://towardsdatascience.com/how-to-build-neural-networks-for-node-classification-from-tabular-data-bdd07ceaef83?source=collection_archive---------6-----------------------#2024-05-13)

## 学习如何在13分钟内使用PyTorch Geometric从CSV文件构建基于图的神经网络

[](https://ds-claudia.medium.com/?source=post_page---byline--bdd07ceaef83--------------------------------)[![Claudia Ng](../Images/81d7927943f38f6303690cfd5676c8fd.png)](https://ds-claudia.medium.com/?source=post_page---byline--bdd07ceaef83--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bdd07ceaef83--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bdd07ceaef83--------------------------------) [Claudia Ng](https://ds-claudia.medium.com/?source=post_page---byline--bdd07ceaef83--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bdd07ceaef83--------------------------------) ·13分钟阅读·2024年5月13日

--

# 介绍

许多数据类型本质上是关系型的，基于图的神经网络是强大的模型，可以学习网络中的复杂模式。

在本文中，我们将介绍如何构建PyTorch `Data`对象以处理同质图数据，然后训练不同类型的神经网络来预测一个节点属于哪个类别。这种预测问题通常被称为节点分类。

我们将使用来自《多尺度属性节点嵌入》论文中的Facebook大型页面-页面网络¹ [数据集](https://snap.stanford.edu/data/facebook-large-page-page-network.html)，该论文由Benedek Rozemberczki、Carl Allen和Rik Sarkar于2019年发表在《复杂网络期刊》上。

该数据集包含22,470个Facebook页面，这些页面根据主题被分类为四个类别之一。每个出版物通过一个大小不一的特征向量来表示。数据集还包含有关Facebook页面的跟随信息，总共有171,992个链接或边缘。

本文详细介绍了从表格数据构建基于图的神经网络的五个步骤：

1) 从URL下载数据，

2) 从表格数据创建PyTorch `Data`对象，
