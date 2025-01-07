# 细看 Scipy 的 Stats 模块 — 第 2 部分

> 原文：[https://towardsdatascience.com/a-closer-look-at-scipys-stats-module-part-2-203d27a71fa3?source=collection_archive---------11-----------------------#2024-09-19](https://towardsdatascience.com/a-closer-look-at-scipys-stats-module-part-2-203d27a71fa3?source=collection_archive---------11-----------------------#2024-09-19)

## 让我们学习 Python 中 scipy.stats 模块的主要方法。

[](https://gustavorsantos.medium.com/?source=post_page---byline--203d27a71fa3--------------------------------)[![Gustavo R Santos](../Images/a19a9f4525cdeb6e7a76cd05246aa622.png)](https://gustavorsantos.medium.com/?source=post_page---byline--203d27a71fa3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--203d27a71fa3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--203d27a71fa3--------------------------------) [Gustavo R Santos](https://gustavorsantos.medium.com/?source=post_page---byline--203d27a71fa3--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--203d27a71fa3--------------------------------) ·阅读时间 6 分钟·2024 年 9 月 19 日

--

![](../Images/1dffb7440ef323eb26ad4a0bb184e8fc.png)

图片由 [Алекс Арцибашев](https://unsplash.com/@lxrcbsv?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来源于 [Unsplash](https://unsplash.com/photos/a-bar-chart-is-shown-on-a-blue-background-vVHXeu0YNbk?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

# 介绍

在上一篇文章中，[细看 Scipy Stats—第 1 部分](/a-closer-look-at-scipys-stats-module-part-1-5071858f32c1)，我们学习了分布、统计学和单样本假设检验。

现在，我们将开始学习这个强大的模块，并检查该包中一些更复杂的功能。

在本文中，我们将学习比较两个样本的统计检验、引导法（Bootstraping）、蒙特卡洛模拟以及使用 Scipy 进行的一些变换。

让我们开始吧。

[](/a-closer-look-at-scipys-stats-module-part-1-5071858f32c1?source=post_page-----203d27a71fa3--------------------------------) [## 细看 Scipy 的 Stats 模块 - 第 1 部分

### 长时间从事该领域的、选择 Python 作为编程语言的数据科学家和程序员…

towardsdatascience.com](/a-closer-look-at-scipys-stats-module-part-1-5071858f32c1?source=post_page-----203d27a71fa3--------------------------------)

# 比较两个样本

比较两个样本是数据科学家常见的任务。在 Scipy 中，当我们想要检查两个不同的样本是否来自相同的分布，从而具有统计上相似的平均值时，可以使用独立样本检验。

```py
# Two samples test: Comparison of means

# Sample 1
samp1 = scs.norm.rvs(loc=2, scale=5…
```
