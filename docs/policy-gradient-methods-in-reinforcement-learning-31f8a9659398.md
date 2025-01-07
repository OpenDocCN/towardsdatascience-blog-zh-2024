# **策略梯度方法**在强化学习中的应用

> 原文：[https://towardsdatascience.com/policy-gradient-methods-in-reinforcement-learning-31f8a9659398?source=collection_archive---------7-----------------------#2024-05-29](https://towardsdatascience.com/policy-gradient-methods-in-reinforcement-learning-31f8a9659398?source=collection_archive---------7-----------------------#2024-05-29)

## 使用策略梯度方法在 Python 中教车穿越山脉：强化学习的数学深度分析

[](https://medium.com/@cristianleo120?source=post_page---byline--31f8a9659398--------------------------------)[![Cristian Leo](../Images/99074292e7dfda50cf50a790b8deda79.png)](https://medium.com/@cristianleo120?source=post_page---byline--31f8a9659398--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--31f8a9659398--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--31f8a9659398--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--31f8a9659398--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--31f8a9659398--------------------------------) ·阅读时长 26 分钟·2024年5月29日

--

![](../Images/bc78b119f99f1be51b59bf704491944c.png)

图像由 DALL-E 生成

假设你正在试图教一只狗去取回一个球。一开始，狗狗根本不知道在你扔球的时候应该做什么。它可能会朝不同的方向跑，忽视球，或者做一些完全无关的事情。你的目标是教会狗狗取回球并把它带回给你。

每次狗狗做某事时，你要么奖励它一块零食，要么什么也不做。如果狗狗跑向球，你就给它奖励。如果它做了其他事情，你就不给奖励。这一套狗狗用来决定该做什么的准则或策略称为“策略”。最初，这些准则是随机的，但通过训练，它们会变得更加专注于取回球。

随着时间的推移，狗狗学会了跑向球并获得奖励。它开始更频繁地采用这种策略，因为它能带来奖励。这本质上就是**策略梯度方法**的工作原理。在本文中，我们将探索它们的机制和数学原理，并使用 OpenAI Gym 来训练一辆车穿越山脉。让我们开始吧！

## **目录**
