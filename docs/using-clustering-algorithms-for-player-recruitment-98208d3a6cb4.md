# 使用聚类算法进行球员招募

> 原文：[`towardsdatascience.com/using-clustering-algorithms-for-player-recruitment-98208d3a6cb4?source=collection_archive---------3-----------------------#2024-04-15`](https://towardsdatascience.com/using-clustering-algorithms-for-player-recruitment-98208d3a6cb4?source=collection_archive---------3-----------------------#2024-04-15)

## 体育分析

## 哪些球员能够帮助富勒姆克服他们的主要缺陷？

[](https://polmarin.medium.com/?source=post_page---byline--98208d3a6cb4--------------------------------)![Pol Marin](https://polmarin.medium.com/?source=post_page---byline--98208d3a6cb4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--98208d3a6cb4--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--98208d3a6cb4--------------------------------) [Pol Marin](https://polmarin.medium.com/?source=post_page---byline--98208d3a6cb4--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--98208d3a6cb4--------------------------------) ·阅读时间：9 分钟·2024 年 4 月 15 日

--

![](img/e92662fdacd94505a70217a036321a53.png)

图片由[马里奥·克拉森](https://unsplash.com/@marioklassen?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

前些天，我很幸运地参加了一个由 xfb Analytics[1]、Transfermarkt[2]和 Football Forum Hungary[3]组织的足球分析黑客松。

由于我们最近获得了分享工作的许可，我决定写一篇关于我使用的方法的博客文章。

目标是选择一支英超球队，分析他们的比赛风格，突出两项缺陷，并准备两份各包含 5 名球员的名单，这些球员可以帮助球队改善。前提是我们必须填补两个不同的位置（因此是“每个位置的两份 5 人名单”）。

然后，从这两份名单中，我们需要选出每个位置的最佳目标，并进一步解释为什么他们是各自位置的最佳人选。

最终结果必须是现实的，并且两名球员的总价格必须低于 6000 万（我们给出了他们的 Transfermarkt 估值）。

现在你已经知道了这个项目的内容，我想谈谈我的方法。我是一个热爱足球的数据科学人员，所以我不得不使用 Python 进行某种技术分析或建模。
