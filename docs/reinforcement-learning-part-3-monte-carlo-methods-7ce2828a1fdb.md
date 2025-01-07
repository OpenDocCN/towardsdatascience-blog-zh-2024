# 强化学习，第三部分：蒙特卡罗方法

> 原文：[https://towardsdatascience.com/reinforcement-learning-part-3-monte-carlo-methods-7ce2828a1fdb?source=collection_archive---------3-----------------------#2024-05-23](https://towardsdatascience.com/reinforcement-learning-part-3-monte-carlo-methods-7ce2828a1fdb?source=collection_archive---------3-----------------------#2024-05-23)

## 从赌场到人工智能：揭示蒙特卡罗方法在复杂环境中的强大力量

[](https://medium.com/@slavahead?source=post_page---byline--7ce2828a1fdb--------------------------------)[![Vyacheslav Efimov](../Images/441e600862b2b93564c6cd81abb0092d.png)](https://medium.com/@slavahead?source=post_page---byline--7ce2828a1fdb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7ce2828a1fdb--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7ce2828a1fdb--------------------------------) [Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--7ce2828a1fdb--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7ce2828a1fdb--------------------------------) ·阅读时间：12分钟·2024年5月23日

--

![](../Images/37d7bbb391066cffc3bf52feb6fac980.png)

# 引言

**强化学习**是机器学习中的一个领域，它引入了一个智能体的概念，该智能体必须在复杂的环境中学习最佳策略。智能体通过其行动学习，依据环境状态获得奖励。强化学习是一个复杂的课题，与机器学习的其他领域有显著区别。因此，只有在给定问题无法通过其他方式解决时，才应使用强化学习。

在本文中，我们将探索蒙特卡罗算法。与标准方法相比，蒙特卡罗算法的优雅之处在于，它们不需要了解环境的动态特性。这一点非常重要，因为在现实生活中，我们通常并不知道状态之间的所有可能转移概率。

> **注意**：为了充分理解本文中包含的概念，强烈建议熟悉强化学习的主要概念，以及本系列前两篇文章中介绍的策略改进方法。
