# 强化学习简介及解决多臂赌博机问题

> 原文：[`towardsdatascience.com/introduction-to-reinforcement-learning-and-solving-the-multi-armed-bandit-problem-e4ae74904e77?source=collection_archive---------4-----------------------#2024-07-30`](https://towardsdatascience.com/introduction-to-reinforcement-learning-and-solving-the-multi-armed-bandit-problem-e4ae74904e77?source=collection_archive---------4-----------------------#2024-07-30)

## 通过 Richard S. Sutton 的《强化学习》及定制 Python 实现进行分析，第一集

[](https://medium.com/@hrmnmichaels?source=post_page---byline--e4ae74904e77--------------------------------)![Oliver S](https://medium.com/@hrmnmichaels?source=post_page---byline--e4ae74904e77--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e4ae74904e77--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e4ae74904e77--------------------------------) [Oliver S](https://medium.com/@hrmnmichaels?source=post_page---byline--e4ae74904e77--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e4ae74904e77--------------------------------)·阅读时长 11 分钟·2024 年 7 月 30 日

--

*强化学习*（RL）是机器学习的一个迷人子领域。你可能已经从一些应用中了解过它，比如围棋[1]、自动驾驶[2]等。

在我看来，同样令人着迷的是 Sutton 和 Barto 的著名书籍《强化学习》[3]。我认为这本书是该主题的绝佳入门，但同时也深入探讨了该领域所有重要的理论话题。不过，书中的内容可能会有些繁重，尤其是在第一次阅读时，可能会显得有些数学化。

![](img/bc65662dc54fac6f6aab98c0120e834d.png)

图片由[卡尔·罗](https://unsplash.com/de/@carltraw?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/de/fotos/grau-rote-arcade-automaten-hv4jH7B_U8Y?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

因此，我决定开始一系列文章，逐章总结这本书。我相信用不同的话语解释内容将大大帮助理解。我还会在 Python 中实现书中的所有（大部分）算法，并将它们应用于通过（曾经的）OpenAI Gym 框架[4]建模的问题和环境。就我所知，这两点目前是独特的，使得这个系列具有独特性。

本文是该系列的第一篇，将简要介绍强化学习的概况，然后快速概述 Sutton 的书籍结构——以及如何……
