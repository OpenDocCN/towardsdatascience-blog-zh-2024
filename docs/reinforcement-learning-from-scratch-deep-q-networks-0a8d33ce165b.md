# 强化学习：深度 Q 网络

> 原文：[https://towardsdatascience.com/reinforcement-learning-from-scratch-deep-q-networks-0a8d33ce165b?source=collection_archive---------1-----------------------#2024-05-23](https://towardsdatascience.com/reinforcement-learning-from-scratch-deep-q-networks-0a8d33ce165b?source=collection_archive---------1-----------------------#2024-05-23)

## 使用深度 Q 网络在 Python 中教一艘航天飞机降落在月球上：强化学习的数学深度解析

[](https://medium.com/@cristianleo120?source=post_page---byline--0a8d33ce165b--------------------------------)[![Cristian Leo](../Images/99074292e7dfda50cf50a790b8deda79.png)](https://medium.com/@cristianleo120?source=post_page---byline--0a8d33ce165b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0a8d33ce165b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0a8d33ce165b--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--0a8d33ce165b--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0a8d33ce165b--------------------------------) ·28分钟阅读·2024年5月23日

--

![](../Images/38970589a005da7933deef0ef58bbb32.png)

由 DALL-E 生成的图像

在强化学习（RL）中，Q 学习是一个基础算法，帮助智能体通过学习一个策略来最大化累积奖励，从而在环境中导航。它通过更新一个行动-价值函数来实现这一点，该函数根据接收到的奖励和未来的估计，估算在给定状态下采取特定行动的预期效用（这听起来不熟悉吗？别担心，我们会在接下来的部分一起讲解）。

然而，传统的 Q 学习存在一些挑战。随着状态空间的增大，它在扩展性上遇到困难，并且在具有连续状态和动作空间的环境中效果较差。这就是深度 Q 网络（DQNs）发挥作用的地方。DQNs 使用神经网络来逼近 Q 值，使得智能体能够有效地处理更大和更复杂的环境。

在本文中，我们将深入探讨深度 Q 网络。我们将探讨 DQN 如何克服传统 Q 学习的局限性，并讨论构成 DQN 的关键组件。我们还将逐步讲解从头开始实现一个 DQN，并将其应用于一个更复杂的环境。通过本文的学习，您将对…
