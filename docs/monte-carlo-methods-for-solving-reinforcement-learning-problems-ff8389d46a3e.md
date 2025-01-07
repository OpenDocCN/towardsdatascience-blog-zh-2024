# 蒙特卡罗方法解决强化学习问题

> 原文：[`towardsdatascience.com/monte-carlo-methods-for-solving-reinforcement-learning-problems-ff8389d46a3e?source=collection_archive---------7-----------------------#2024-09-04`](https://towardsdatascience.com/monte-carlo-methods-for-solving-reinforcement-learning-problems-ff8389d46a3e?source=collection_archive---------7-----------------------#2024-09-04)

## 剖析理查德·S·萨顿的《强化学习》并结合定制的 Python 实现，第 III 集

[](https://medium.com/@hrmnmichaels?source=post_page---byline--ff8389d46a3e--------------------------------)![Oliver S](https://medium.com/@hrmnmichaels?source=post_page---byline--ff8389d46a3e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ff8389d46a3e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ff8389d46a3e--------------------------------) [Oliver S](https://medium.com/@hrmnmichaels?source=post_page---byline--ff8389d46a3e--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ff8389d46a3e--------------------------------) ·18 分钟阅读·2024 年 9 月 4 日

--

我们继续深入探索 Sutton 关于强化学习的伟大著作 [1]，并在此重点讨论蒙特卡罗（MC）方法。这些方法仅通过经验学习，即不需要任何环境模型，例如我们在上一篇文章中介绍的动态规划（DP）方法。

这非常有吸引力——因为通常模型是未知的，或者很难建模转移概率。考虑一下 [二十一点](https://en.wikipedia.org/wiki/Blackjack) 游戏：尽管我们完全理解游戏规则，通过 DP 方法解决它将非常繁琐——我们需要计算各种概率，例如，给定当前打出的牌，“黑杰克”出现的可能性有多大，是否有可能再发一张七点牌……通过 MC 方法，我们无需处理这些问题，只需玩游戏并从经验中学习。

![](img/08c697f6fd32b28f30fd93d741dfe436.png)

图片由 [Jannis Lucas](https://unsplash.com/@jannis_lucas?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来源于 [Unsplash](https://unsplash.com/photos/white-building-facade-Y4L7b_ilnEc?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

由于不使用模型，蒙特卡罗方法（MC）是无偏的。它们在概念上简单且易于理解，但表现出较高的方差，且无法通过迭代方式（如自助法）解决。

如前所述，在这里我们将介绍这些方法，内容基于 Sutton 书中的第五章…
