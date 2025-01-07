# 强化学习101：构建一个RL智能体

> 原文：[https://towardsdatascience.com/reinforcement-learning-101-building-a-rl-agent-0431984ba178?source=collection_archive---------1-----------------------#2024-02-19](https://towardsdatascience.com/reinforcement-learning-101-building-a-rl-agent-0431984ba178?source=collection_archive---------1-----------------------#2024-02-19)

## 解读强化学习背后的数学，介绍RL框架，并用Python从头构建一个RL模拟。

[](https://medium.com/@cristianleo120?source=post_page---byline--0431984ba178--------------------------------)[![Cristian Leo](../Images/99074292e7dfda50cf50a790b8deda79.png)](https://medium.com/@cristianleo120?source=post_page---byline--0431984ba178--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0431984ba178--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0431984ba178--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--0431984ba178--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0431984ba178--------------------------------) ·19分钟阅读·2024年2月19日

--

![](../Images/7d3eceb7c2acfe492cfef92af360bd29.png)

图片由DALL-E生成

强化学习（RL）是人工智能领域中的一个关键元素，以其独特的方式教授机器通过与环境的互动来进行决策。在本文中，我们将深入探讨RL的核心机制。我们将分解其核心概念，突出其广泛的应用，解读其数学原理，并引导你从零开始构建一个RL模型。

**索引**

**·** [**强化学习简介**](#f6db)

∘ [什么是强化学习？](#b401)

∘ [它是如何工作的？](#5978)

**·** [**RL框架**](#6322)

∘ [状态](#2c62)

∘ [动作](#a546)

∘ [奖励](#c2ef)

**·** [**回合和策略的概念**](#f897)

∘ [回合](#04c4)

∘ [策略](#09a4)

**·** [**RL问题的数学公式化**](#a7f1)

∘ [目标函数](#5fb3)

∘ [回报（累计奖励）](#d269)

∘ [折扣](#71ce)
