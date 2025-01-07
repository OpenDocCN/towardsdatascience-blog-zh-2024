# 使用替代模型进行优化，通过符号回归实现

> 原文：[`towardsdatascience.com/optimization-with-surrogate-models-via-symbolic-regression-38f9b7536a36?source=collection_archive---------3-----------------------#2024-01-19`](https://towardsdatascience.com/optimization-with-surrogate-models-via-symbolic-regression-38f9b7536a36?source=collection_archive---------3-----------------------#2024-01-19)

## **通过符号回归方法识别代数替代模型来优化黑箱系统的一种可能性。**

[](https://medium.com/@mit.forster?source=post_page---byline--38f9b7536a36--------------------------------)![Tim Forster](https://medium.com/@mit.forster?source=post_page---byline--38f9b7536a36--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--38f9b7536a36--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--38f9b7536a36--------------------------------) [Tim Forster](https://medium.com/@mit.forster?source=post_page---byline--38f9b7536a36--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--38f9b7536a36--------------------------------) ·16 分钟阅读·2024 年 1 月 19 日

--

![](img/f9de50a25cdc455a33647209b4aa3dc2.png)

图片由 Jeremy Bishop 提供，来自 Unsplash

> 注：如果你没有 Medium 订阅，你可以[免费阅读本文](https://towardsdatascience.com/optimization-with-surrogate-models-via-symbolic-regression-38f9b7536a36?source=collection_archive---------3-----------------------#2024-01-19)（如果你有订阅，请继续阅读，谢谢！🥰）

执行优化是一个非常有趣的任务。在我们的日常生活中，我们可能会对如何以最短的时间到达工作地点感兴趣，或者可能会关注如何调整我们研磨咖啡的最佳颗粒大小，以制作一杯非常美味的咖啡 ☕。各行各业也在关注优化，例如供应链、碳排放或废物积累等问题。

设置优化的方式有很多，具体取决于特定情况的不同。为了本篇文章，我将这些情况分为两部分：

一方面，我们可能了解驱动研究系统的物理、化学或生物学原理。基于这些知识，我们可以建立[代数方程](https://en.wikipedia.org/wiki/Algebraic_equation)，准确地描述我们观察到的现象（[第一性原理](https://en.wikipedia.org/wiki/First_principle)）。这些情况允许使用现成的求解器，如[GLPK](https://www.gnu.org/software/glpk/)、[BARON](https://link.springer.com/article/10.1007/BF00138693)、[ANTIGONE](https://link.springer.com/article/10.1007/s10898-014-0166-2)、[SBB](https://www.gams.com/latest/docs/S_SBB.html)或其他，因为我们有…
