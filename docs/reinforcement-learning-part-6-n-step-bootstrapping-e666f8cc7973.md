# 强化学习，第6部分：n步自举法

> 原文：[https://towardsdatascience.com/reinforcement-learning-part-6-n-step-bootstrapping-e666f8cc7973?source=collection_archive---------7-----------------------#2024-08-07](https://towardsdatascience.com/reinforcement-learning-part-6-n-step-bootstrapping-e666f8cc7973?source=collection_archive---------7-----------------------#2024-08-07)

## **拓展边界：泛化时间差分算法**

[](https://medium.com/@slavahead?source=post_page---byline--e666f8cc7973--------------------------------)[![Vyacheslav Efimov](../Images/441e600862b2b93564c6cd81abb0092d.png)](https://medium.com/@slavahead?source=post_page---byline--e666f8cc7973--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e666f8cc7973--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e666f8cc7973--------------------------------) [Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--e666f8cc7973--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e666f8cc7973--------------------------------) ·6分钟阅读·2024年8月7日

--

![](../Images/0532e48851a49d92a2d95a3ab4025a30.png)

# 介绍

**强化学习**是机器学习中的一个领域，引入了代理在复杂环境中学习最优策略的概念。代理通过其行动从环境的状态中获得奖励，从而进行学习。强化学习是一个具有挑战性的话题，与机器学习的其他领域有显著区别。

强化学习的独特之处在于，相同的算法可以用来帮助代理适应完全不同、未知且复杂的条件。

> **注意**：为了充分理解本文中包含的概念，强烈建议熟悉之前文章中介绍的[蒙特卡洛方法](https://medium.com/towards-data-science/reinforcement-learning-part-3-monte-carlo-methods-7ce2828a1fdb)和[时间差分学习](https://medium.com/towards-data-science/reinforcement-learning-part-5-temporal-difference-learning-cacf7854fe0c)的基础知识。

[](/reinforcement-learning-part-3-monte-carlo-methods-7ce2828a1fdb?source=post_page-----e666f8cc7973--------------------------------) [## 强化学习，第3部分：蒙特卡洛方法

### 从赌场到人工智能：揭示蒙特卡洛方法在复杂环境中的威力

towardsdatascience.com](/reinforcement-learning-part-3-monte-carlo-methods-7ce2828a1fdb?source=post_page-----e666f8cc7973--------------------------------)
