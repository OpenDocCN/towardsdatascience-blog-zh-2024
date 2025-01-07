# 强化学习 101：构建一个 RL 智能体

> 原文：[`towardsdatascience.com/reinforcement-learning-101-building-a-rl-agent-0431984ba178?source=collection_archive---------1-----------------------#2024-02-19`](https://towardsdatascience.com/reinforcement-learning-101-building-a-rl-agent-0431984ba178?source=collection_archive---------1-----------------------#2024-02-19)

## 解读强化学习背后的数学，介绍 RL 框架，并用 Python 从头构建一个 RL 模拟。

[](https://medium.com/@cristianleo120?source=post_page---byline--0431984ba178--------------------------------)![Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--0431984ba178--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0431984ba178--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0431984ba178--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--0431984ba178--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0431984ba178--------------------------------) ·19 分钟阅读·2024 年 2 月 19 日

--

![](img/7d3eceb7c2acfe492cfef92af360bd29.png)

图片由 DALL-E 生成

强化学习（RL）是人工智能领域中的一个关键元素，以其独特的方式教授机器通过与环境的互动来进行决策。在本文中，我们将深入探讨 RL 的核心机制。我们将分解其核心概念，突出其广泛的应用，解读其数学原理，并引导你从零开始构建一个 RL 模型。

**索引**

**·** **强化学习简介**

∘ 什么是强化学习？

∘ 它是如何工作的？

**·** **RL 框架**

∘ 状态

∘ 动作

∘ 奖励

**·** **回合和策略的概念**

∘ 回合

∘ 策略

**·** **RL 问题的数学公式化**

∘ 目标函数

∘ 回报（累计奖励）

∘ 折扣
