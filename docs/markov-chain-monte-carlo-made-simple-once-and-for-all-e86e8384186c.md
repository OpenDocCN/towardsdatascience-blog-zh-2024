# 马尔可夫链蒙特卡洛：一劳永逸的简化解释

> 原文：[`towardsdatascience.com/markov-chain-monte-carlo-made-simple-once-and-for-all-e86e8384186c?source=collection_archive---------0-----------------------#2024-03-01`](https://towardsdatascience.com/markov-chain-monte-carlo-made-simple-once-and-for-all-e86e8384186c?source=collection_archive---------0-----------------------#2024-03-01)

## 数据科学

## MCMC 介绍，将其拆解为最简单的部分

[](https://polmarin.medium.com/?source=post_page---byline--e86e8384186c--------------------------------)![Pol Marin](https://polmarin.medium.com/?source=post_page---byline--e86e8384186c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e86e8384186c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e86e8384186c--------------------------------) [Pol Marin](https://polmarin.medium.com/?source=post_page---byline--e86e8384186c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e86e8384186c--------------------------------) ·阅读时间：7 分钟·2024 年 3 月 1 日

--

![](img/2e2af6b85503a7f9363f34f4efe0762f.png)

图片由 [Karine Avetisyan](https://unsplash.com/@kar111?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我最近发布了一篇文章，使用贝叶斯推断和马尔可夫链蒙特卡洛（MCMC）来预测冠军联赛 16 强的获胜者。在那篇文章中，我试图相对深入地解释贝叶斯统计，但没有详细讲解 MCMC，以避免文章过于冗长。文章链接：

[](/using-bayesian-modeling-to-predict-the-champions-league-8ebb069006ba?source=post_page-----e86e8384186c--------------------------------) [## 使用贝叶斯建模预测冠军联赛

### 贝叶斯推断在现实世界中的应用

[towardsdatascience.com](https://towardsdatascience.com/using-bayesian-modeling-to-predict-the-champions-league-8ebb069006ba?source=post_page-----e86e8384186c--------------------------------)

因此，我决定专门写一篇文章，介绍马尔可夫链蒙特卡洛方法，供那些有兴趣了解其数学原理以及何时能发挥作用的人阅读。

为了应对这篇文章，我将采用分而治之的策略：将这个术语拆解成最简单的部分，分别解释它们，然后解决整体问题。我们将讨论的内容如下：

+   蒙特卡洛方法

+   随机过程

+   马尔可夫链

+   MCMC

## 蒙特卡洛方法
