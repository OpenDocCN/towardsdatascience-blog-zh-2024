# 强化学习，第四部分：蒙特卡洛控制

> 原文：[`towardsdatascience.com/reinforcement-learning-part-4-monte-carlo-control-ae0a7f29920b?source=collection_archive---------4-----------------------#2024-06-11`](https://towardsdatascience.com/reinforcement-learning-part-4-monte-carlo-control-ae0a7f29920b?source=collection_archive---------4-----------------------#2024-06-11)

## 利用蒙特卡洛算法发现最佳策略

[](https://medium.com/@slavahead?source=post_page---byline--ae0a7f29920b--------------------------------)![Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--ae0a7f29920b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ae0a7f29920b--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ae0a7f29920b--------------------------------) [Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--ae0a7f29920b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ae0a7f29920b--------------------------------) ·阅读时间：13 分钟·2024 年 6 月 11 日

--

![](img/ccda9eebd2feeb01b4ef59b81b9dabcb.png)

# 引言

**强化学习**是机器学习中的一个领域，它引入了智能体的概念，智能体必须在复杂的环境中学习最优策略。智能体通过其行为从环境的状态中获得奖励，进而学习。强化学习是一个困难的话题，与机器学习的其他领域有很大的不同。因此，只有在某个问题无法通过其他方式解决时，才应该使用强化学习。

强化学习的一个令人难以置信的地方在于，相同的算法可以用来使智能体适应完全不同、未知且复杂的环境条件。

特别地，蒙特卡洛算法不需要任何关于环境动态的知识。这是一个非常有用的特性，因为在现实生活中，我们通常无法获得这些信息。在上一篇文章中，我们讨论了蒙特卡洛方法的基本思想，这次我们将重点介绍一些特殊方法来改进这些算法。

> **注意**：为了充分理解本文所涉及的概念，强烈建议先熟悉在[第三部分](https://medium.com/towards-data-science/reinforcement-learning-part-3-monte-carlo-methods-7ce2828a1fdb)中介绍的蒙特卡洛算法的主要概念…
