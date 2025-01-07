# K-Means 聚类背后的数学与代码

> 原文：[https://towardsdatascience.com/the-math-and-code-behind-k-means-clustering-795582423666?source=collection_archive---------3-----------------------#2024-02-13](https://towardsdatascience.com/the-math-and-code-behind-k-means-clustering-795582423666?source=collection_archive---------3-----------------------#2024-02-13)

## 为什么 K-Means 是无监督学习中最受欢迎的算法？让我们深入研究它的数学原理，并从零开始构建它。

[](https://medium.com/@cristianleo120?source=post_page---byline--795582423666--------------------------------)[![Cristian Leo](../Images/99074292e7dfda50cf50a790b8deda79.png)](https://medium.com/@cristianleo120?source=post_page---byline--795582423666--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--795582423666--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--795582423666--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--795582423666--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--795582423666--------------------------------) ·阅读时间 19 分钟·2024年2月13日

--

![](../Images/ece4da8af67137663cc11fb8bd0083ac.png)

图像由 DALL-E 生成

K-means 聚类是机器学习中的一项基础技术，因为它通过简单的方式组织复杂数据。在本文中，我们将探讨算法的核心。我们将深入研究它的应用，剖析背后的数学原理，从零开始构建它，并讨论它在数据科学这一快速发展的领域中的相关性。

**索引**

**·** [**1: 理解基础知识**](#f655)

∘ [1.1: 什么是 K-Means 聚类？](#81af)

∘ [1.2: K-Means 是如何工作的？](#1adb)

**·** [**2: 实现 K-Means**](#5a8e)

∘ [2.1: K-Means 背后的数学原理](#4fbe)

∘ [2.2: 如何选择最优的聚类数目](#cdd6)

**·** [**3: K-Means 在实践中的应用**](#11b2)

∘ [3.1 从零开始在 Python 中实现 K-Means](#7dd0)

∘ [3.2 使用 Scikit-Learn 实现 K-Means](#7c94)

**·** [**4: 优势与挑战**](#53ed)

∘ [4.1 使用 K-Means 的优势](#163c)

∘ [4.2 克服 K-Means 的局限性](#4ae7)
