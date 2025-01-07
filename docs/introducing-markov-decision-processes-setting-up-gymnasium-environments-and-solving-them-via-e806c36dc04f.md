# 介绍马尔可夫决策过程，设置 Gymnasium 环境并通过动态规划方法求解

> 原文：[`towardsdatascience.com/introducing-markov-decision-processes-setting-up-gymnasium-environments-and-solving-them-via-e806c36dc04f?source=collection_archive---------4-----------------------#2024-08-26`](https://towardsdatascience.com/introducing-markov-decision-processes-setting-up-gymnasium-environments-and-solving-them-via-e806c36dc04f?source=collection_archive---------4-----------------------#2024-08-26)

## 通过理查德·S·萨顿的《强化学习》一书和自定义的 Python 实现，解析“强化学习”，第二集

[](https://medium.com/@hrmnmichaels?source=post_page---byline--e806c36dc04f--------------------------------)![Oliver S](https://medium.com/@hrmnmichaels?source=post_page---byline--e806c36dc04f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e806c36dc04f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e806c36dc04f--------------------------------) [Oliver S](https://medium.com/@hrmnmichaels?source=post_page---byline--e806c36dc04f--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e806c36dc04f--------------------------------) ·阅读时间：12 分钟·2024 年 8 月 26 日

--

在 [上一篇文章](https://medium.com/towards-data-science/introduction-to-reinforcement-learning-and-solving-the-multi-armed-bandit-problem-e4ae74904e77) 中，我们开始了关于强化学习（RL）系列的第一篇文章，依据萨顿的经典著作[1]。在那篇文章中，我们介绍了强化学习的一般概念，并讨论了多臂赌博机问题，这是一个*非关联*的玩具问题。

在这里，我们将在此基础上进一步扩展——但会有显著的提升。特别是，我们将引入我们的第一个*关联*问题，这对许多读者来说可能更像是“真实”的强化学习（RL）——并介绍一种简单但通用的解决技巧。此外，我们还将介绍 Gymnasium [2]，这是一个强大的库，提供了多种环境（例如，Atari 或 MuJoCo 游戏），并允许我们快速实验解决这些问题。

![](img/7ea806371c177ee506c0b0a5ecfb843b.png)

图片来源：[Adarsh Kummur](https://unsplash.com/@akummur?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在 [Unsplash](https://unsplash.com/photos/wilted-tree-during-daytime-zThTy8rPPsY?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

前面提到的关联设置是强化学习（RL）中的“标准”：与之前介绍的非关联设置不同，后者只有一个状态，我们只需决定采取什么行动，而在这里，我们有多个状态——对于每个状态，我们可能会选择不同的最佳行动。
