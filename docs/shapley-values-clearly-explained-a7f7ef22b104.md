# Shapley 值清晰解释

> 原文：[`towardsdatascience.com/shapley-values-clearly-explained-a7f7ef22b104?source=collection_archive---------0-----------------------#2024-02-04`](https://towardsdatascience.com/shapley-values-clearly-explained-a7f7ef22b104?source=collection_archive---------0-----------------------#2024-02-04)

## 公平地将团队成果分配给每个成员

[](https://dr-robert-kuebler.medium.com/?source=post_page---byline--a7f7ef22b104--------------------------------)![Dr. Robert Kübler](https://dr-robert-kuebler.medium.com/?source=post_page---byline--a7f7ef22b104--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a7f7ef22b104--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a7f7ef22b104--------------------------------) [Dr. Robert Kübler](https://dr-robert-kuebler.medium.com/?source=post_page---byline--a7f7ef22b104--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a7f7ef22b104--------------------------------) ·12 分钟阅读·2024 年 2 月 4 日

--

![](img/5e699dc2a90a6aa24698fd8ed6f0fb16.png)

图片由 [Vadim Sherbakov](https://unsplash.com/@madebyvadim?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

上次你和朋友们一起合作并取得了一些伟大的成绩是什么时候？无论是赢得一场比赛、完成一个工作项目，还是在 Kaggle 比赛中进入前三。如果你不记得了（真可怜），那和朋友们度过一个愉快的夜晚怎么样？想象一下：度过了一个精彩的晚上，随后大家一起搭出租车回家，却迎来了一个不小的出租车账单。在这样的时刻，你或许会开始思考：

> 我们如何能将团队成果公平地分配给每个成员？

一个*团队*也可以是**机器学习模型**的特征，团队的成果就是模型的预测结果。在这种情况下，了解**每个特征对最终预测的贡献有多大**是非常有趣的。这正是数据科学家通常关注的内容。

## 公平还是平均？

举个例子，假设你和另外两位朋友组成团队参加比赛，表现优异并作为团队获得了 12,000 欧元。你该如何**公平**地分配这笔钱呢？**平均**分配没有问题，每个人获得 4,000 欧元。但我们都知道，通常有些人对团队的成功贡献要比其他人多。那么……
