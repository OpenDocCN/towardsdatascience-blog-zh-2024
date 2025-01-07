# GNN 的表达能力 — 消息传递神经网络

> 原文：[`towardsdatascience.com/the-expressive-power-of-gnns-the-message-passing-neural-network-e4b5f8f6c01f?source=collection_archive---------5-----------------------#2024-09-02`](https://towardsdatascience.com/the-expressive-power-of-gnns-the-message-passing-neural-network-e4b5f8f6c01f?source=collection_archive---------5-----------------------#2024-09-02)

## 表达性 GNN 系列

## 引入 MPNN 架构与 PyTorch Geometric，连接点滴，进行图神经网络模型的理论分析

[](https://medium.com/@giuseppefutia?source=post_page---byline--e4b5f8f6c01f--------------------------------)![Giuseppe Futia](https://medium.com/@giuseppefutia?source=post_page---byline--e4b5f8f6c01f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e4b5f8f6c01f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e4b5f8f6c01f--------------------------------) [Giuseppe Futia](https://medium.com/@giuseppefutia?source=post_page---byline--e4b5f8f6c01f--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e4b5f8f6c01f--------------------------------) ·18 分钟阅读·2024 年 9 月 2 日

--

# 介绍

图神经网络（GNNs）是强大的架构，旨在建模和分析以图结构组织的数据。这些模型能够有效捕捉图中信息的模式，从而支持一系列下游任务，包括节点分类、链接预测和图回归。

在本系列的上一篇文章中，我们介绍了*图同构*的概念，这对于阐明不同或等价关系结构的概念至关重要。

[](/the-expressive-power-of-gnns-introduction-and-foundations-5cdb4bca6ae3?source=post_page-----e4b5f8f6c01f--------------------------------) ## GNN 的表达能力 — 介绍与基础

### 连接点滴，进行图神经网络模型的理论分析

towardsdatascience.com

根据图同构原则，我们可以为任何在图上运行的机器学习模型设定要求：

+   为同构图生成相同的表示。

+   通过不同的关系结构来区分图。
