# 解密图神经网络

> 原文：[https://towardsdatascience.com/demystifying-graph-neural-networks-b36229ef6556?source=collection_archive---------7-----------------------#2024-01-05](https://towardsdatascience.com/demystifying-graph-neural-networks-b36229ef6556?source=collection_archive---------7-----------------------#2024-01-05)

## 揭示一种新兴深度学习算法的力量和应用

[](https://mohamed-mamoun-berrada.medium.com/?source=post_page---byline--b36229ef6556--------------------------------)[![穆罕默德·马蒙·贝拉达](../Images/f3e2400714558157a78bcbedc74fc19f.png)](https://mohamed-mamoun-berrada.medium.com/?source=post_page---byline--b36229ef6556--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b36229ef6556--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b36229ef6556--------------------------------) [穆罕默德·马蒙·贝拉达](https://mohamed-mamoun-berrada.medium.com/?source=post_page---byline--b36229ef6556--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b36229ef6556--------------------------------) ·阅读时长6分钟·2024年1月5日

--

![](../Images/1363f5defe406c8f1ec4109e344ab818.png)

由[莫里茨·金德勒](https://unsplash.com/@moritz_photography?utm_source=medium&utm_medium=referral)拍摄，图片来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

通过这篇文章，我旨在向大家介绍一种日益流行的深度学习算法——图神经网络（GNNs）。GNNs正逐渐从研究领域走向实际应用，已经在现实世界的问题上展示了令人印象深刻的成果，显示出它们巨大的潜力。本文的主要目标是解密这一算法。如果到最后，你能回答像“为什么要使用GNN？”“GNN是如何工作的？”这样的问题，我会认为我的任务已经完成。

在深入讨论之前，有必要回顾两个与我们话题密切相关的概念：

***图与嵌入***

## 计算机科学中的图

让我们首先简要回顾一下图是什么。图在无数领域中都有应用，特别是在计算机科学中，图是一种由两个元素组成的数据结构：一组顶点或节点，以及一组连接这些节点的边。

图可以是有向图或无向图。有向图是指边具有方向性的图，如下所示。

所以，图是对象（节点）之间关系（边）的表示。
