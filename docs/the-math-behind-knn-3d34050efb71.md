# K-最近邻的数学原理

> 原文：[`towardsdatascience.com/the-math-behind-knn-3d34050efb71?source=collection_archive---------0-----------------------#2024-02-06`](https://towardsdatascience.com/the-math-behind-knn-3d34050efb71?source=collection_archive---------0-----------------------#2024-02-06)

## 为什么 K-最近邻（K-Nearest Neighbors, KNN）是最流行的机器学习算法之一？让我们通过深入了解其数学原理，从零开始构建它来理解它。

[](https://medium.com/@cristianleo120?source=post_page---byline--3d34050efb71--------------------------------)![Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--3d34050efb71--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3d34050efb71--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3d34050efb71--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--3d34050efb71--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3d34050efb71--------------------------------) ·阅读时间 18 分钟·2024 年 2 月 6 日

--

![](img/05b8a90cad5a6ede795e4c0e91c44084.png)

图片由 DALL-E 生成

K-最近邻（KNN）是最流行的机器学习算法之一。它的简单性、多功能性和适应性使其成为常见选择。但是，为什么它如此流行？为什么它表现得如此出色？本文将揭示 KNN 的工作原理，并解释为什么它是许多数据科学家首选的工具。我们将探讨它的应用、数学原理，手把手从零开始构建它，并讨论在一个由最新技术推动的领域中，为什么 KNN 依然保持其相关性并被广泛使用。

**目录**

·**1: 理解基础知识**

∘ 1.1: 什么是 K-最近邻？

∘ 1.2: KNN 是如何工作的？

**·** **2: 实现 KNN**

∘ 2.1: KNN 背后的数学

∘ 2.2: 选择正确的 K 值

∘ 2.3: 如何选择合适的距离度量

**·** **3: KNN 的实践应用**

∘ 3.1 从零开始在 Python 中实现 KNN

∘ 3.2 使用 Scikit-Learn 实现 KNN

**·** **4: 优势与挑战**

∘ 4.1 使用 KNN 的好处

∘ 4.2…
