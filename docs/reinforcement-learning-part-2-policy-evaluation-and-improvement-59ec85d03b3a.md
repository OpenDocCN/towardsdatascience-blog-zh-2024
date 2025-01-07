# 强化学习第二部分：策略评估与改进

> 原文：[https://towardsdatascience.com/reinforcement-learning-part-2-policy-evaluation-and-improvement-59ec85d03b3a?source=collection_archive---------1-----------------------#2024-04-23](https://towardsdatascience.com/reinforcement-learning-part-2-policy-evaluation-and-improvement-59ec85d03b3a?source=collection_archive---------1-----------------------#2024-04-23)

## 从数据到决策：通过策略改进方法最大化奖励以获得最佳策略

[](https://medium.com/@slavahead?source=post_page---byline--59ec85d03b3a--------------------------------)[![Vyacheslav Efimov](../Images/441e600862b2b93564c6cd81abb0092d.png)](https://medium.com/@slavahead?source=post_page---byline--59ec85d03b3a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--59ec85d03b3a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--59ec85d03b3a--------------------------------) [Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--59ec85d03b3a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--59ec85d03b3a--------------------------------) ·13分钟阅读·2024年4月23日

--

![](../Images/9d0b6d24534b97065ed2a415650d931e.png)

# 引言

**强化学习**是机器学习中的一个领域，引入了智能体的概念，智能体必须在复杂环境中学习最佳策略。智能体根据其行动得到的奖励以及环境的状态来学习。强化学习是一个复杂的主题，与机器学习的其他领域有显著的不同。这就是为什么它只有在其他方法无法解决特定问题时才应该使用。

强化学习的惊人灵活性在于相同的算法可以用来使智能体适应完全不同、未知和复杂的环境条件。

> **注意**。为了充分理解本文所包含的思想，强烈建议熟悉本文系列[第一部分](https://medium.com/towards-data-science/reinforcement-learning-introduction-and-main-concepts-48ea997c850c)中介绍的强化学习的主要概念。
