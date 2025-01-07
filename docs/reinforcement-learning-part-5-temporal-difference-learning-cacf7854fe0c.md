# 强化学习，第五部分：时间差学习

> 原文：[`towardsdatascience.com/reinforcement-learning-part-5-temporal-difference-learning-cacf7854fe0c?source=collection_archive---------4-----------------------#2024-07-13`](https://towardsdatascience.com/reinforcement-learning-part-5-temporal-difference-learning-cacf7854fe0c?source=collection_archive---------4-----------------------#2024-07-13)

## 智能地协同动态规划和蒙特卡洛算法

[](https://medium.com/@slavahead?source=post_page---byline--cacf7854fe0c--------------------------------)![Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--cacf7854fe0c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cacf7854fe0c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cacf7854fe0c--------------------------------) [Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--cacf7854fe0c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cacf7854fe0c--------------------------------) ·15 分钟阅读·2024 年 7 月 13 日

--

![](img/6f0c189ccd16283132ffcd5a786aa783.png)

# 引言

**强化学习**是机器学习的一个领域，引入了代理在复杂环境中学习最优策略的概念。代理通过根据环境状态采取行动来获得奖励，从而学习。强化学习是一个具有挑战性的主题，与机器学习的其他领域有显著不同。

强化学习的显著特点是，同样的算法可以用来使代理适应完全不同、未知且复杂的环境条件。

> **注意**：为了充分理解本文所包含的概念，强烈建议先了解之前文章中讨论的[动态规划](https://medium.com/towards-data-science/reinforcement-learning-part-2-policy-evaluation-and-improvement-59ec85d03b3a)和[蒙特卡洛方法](https://medium.com/towards-data-science/reinforcement-learning-part-3-monte-carlo-methods-7ce2828a1fdb)。

[](/reinforcement-learning-part-2-policy-evaluation-and-improvement-59ec85d03b3a?source=post_page-----cacf7854fe0c--------------------------------) ## 强化学习，第二部分：策略评估与改进

### 从数据到决策：通过策略改进方法最大化奖励，寻找最优策略

towardsdatascience.com
