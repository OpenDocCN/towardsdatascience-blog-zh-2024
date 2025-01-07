# 粒子群优化

> 原文：[`towardsdatascience.com/particle-swarm-optimization-b869231c57fe?source=collection_archive---------7-----------------------#2024-01-10`](https://towardsdatascience.com/particle-swarm-optimization-b869231c57fe?source=collection_archive---------7-----------------------#2024-01-10)

## 优化任意函数的最迷人的方式

[](https://dr-robert-kuebler.medium.com/?source=post_page---byline--b869231c57fe--------------------------------)![Dr. Robert Kübler](https://dr-robert-kuebler.medium.com/?source=post_page---byline--b869231c57fe--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b869231c57fe--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b869231c57fe--------------------------------) [Dr. Robert Kübler](https://dr-robert-kuebler.medium.com/?source=post_page---byline--b869231c57fe--------------------------------)

·发布于[数据科学探索](https://towardsdatascience.com/?source=post_page---byline--b869231c57fe--------------------------------) ·阅读时间 7 分钟·2024 年 1 月 10 日

--

![](img/f3784a1f79a963705c99d445f0ad998d.png)

图片由[James Wainscoat](https://unsplash.com/@tumbao1949?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

无论我们处理的是机器学习、运筹学还是其他数值领域，我们都有一个共同的任务，那就是**优化函数**。根据不同的领域，出现了一些常用的方法：

+   在机器学习中，当训练神经网络时，我们通常使用梯度下降。这之所以有效，是因为我们处理的函数是可微的（至少在几乎所有地方——见 ReLU）。

+   在运筹学中，我们经常处理可以通过线性（或凸）规划解决的线性（或凸）优化问题。

如果我们能够应用这些方法，那总是非常棒的。然而，对于优化一般函数——所谓的**黑箱优化**——我们必须借助其他技术。其中一个特别有趣的技术是所谓的**粒子群优化**，在本文中，我将向你展示它是如何工作的以及如何实现它。

请注意，这些算法并不总是能给出最佳解，因为它是一种高度随机和启发式的算法。尽管如此，它仍然是你工具箱中一个很好的技术，当你遇到难以优化的函数时，应该尝试一下！

# 粒子群优化
