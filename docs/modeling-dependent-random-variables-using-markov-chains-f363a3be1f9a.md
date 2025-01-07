# 使用马尔可夫链建模依赖随机变量

> 原文：[`towardsdatascience.com/modeling-dependent-random-variables-using-markov-chains-f363a3be1f9a?source=collection_archive---------2-----------------------#2024-02-23`](https://towardsdatascience.com/modeling-dependent-random-variables-using-markov-chains-f363a3be1f9a?source=collection_archive---------2-----------------------#2024-02-23)

## 纠正独立性违背的依赖模型

[](https://romanmichaelpaolucci.medium.com/?source=post_page---byline--f363a3be1f9a--------------------------------)![Roman Paolucci](https://romanmichaelpaolucci.medium.com/?source=post_page---byline--f363a3be1f9a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f363a3be1f9a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f363a3be1f9a--------------------------------) [Roman Paolucci](https://romanmichaelpaolucci.medium.com/?source=post_page---byline--f363a3be1f9a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f363a3be1f9a--------------------------------) ·阅读时长：15 分钟·2024 年 2 月 23 日

--

![](img/3440e542aeebecf661620ca654f5004c.png)

摄影：Nick Collins：[`www.pexels.com/photo/blue-abstract-painting-1292996/`](https://www.pexels.com/photo/blue-abstract-painting-1292996/)

在我之前讨论的随机变量参数的最大似然估计一文中，我们扮演了医院风险经理、高级医生统计学家、数据科学护士（我依然*一点也不清楚*谁会负责这个）等角色，并开发了一个简单的概率模型来估计在没有足够床位容纳新患者时的风险。为了实现这一目标，我们做出了以下假设：

+   *假设所有入住医院的患者当天会出院*

+   *假设每天入住的患者是相互独立的*

虽然这些假设不太现实，但它们使我们能够将某一天的患者数建模为一个泊松随机变量（见[常见随机变量](https://medium.com/quant-guild/common-random-variables-f30c537a01e4)），该变量具有明确的分布函数，我们可以用它来估计无法容纳新患者的概率。

> 稍微偏离一下话题——当然，泊松随机变量由*lambda*参数化，它模型化了某一天患者的期望值和方差。在上一篇文章中，我们花了大部分时间讨论了如何利用最大似然法（maximum likelihood）根据一组观察数据来估计这个*最佳*统计量。
