# 强化学习，第一部分：简介与主要概念

> 原文：[https://towardsdatascience.com/reinforcement-learning-introduction-and-main-concepts-48ea997c850c?source=collection_archive---------2-----------------------#2024-04-09](https://towardsdatascience.com/reinforcement-learning-introduction-and-main-concepts-48ea997c850c?source=collection_archive---------2-----------------------#2024-04-09)

## 迈出进入强化学习世界的第一步

[](https://medium.com/@slavahead?source=post_page---byline--48ea997c850c--------------------------------)[![Vyacheslav Efimov](../Images/441e600862b2b93564c6cd81abb0092d.png)](https://medium.com/@slavahead?source=post_page---byline--48ea997c850c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--48ea997c850c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--48ea997c850c--------------------------------) [Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--48ea997c850c--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--48ea997c850c--------------------------------) ·阅读时间11分钟·2024年4月9日

--

![](../Images/2e6592d197bb45d036c0adabc24facf3.png)

# 介绍

**强化学习**是机器学习的一个特殊领域，与监督学习或无监督学习中使用的经典方法有很大不同。

> 最终目标是开发一个所谓的**智能体**，使其能够在环境中执行最优的动作。最初，智能体通常表现得非常差，但随着时间的推移，它通过与环境互动，采用**试错法**逐步调整策略。

强化学习的魅力在于，同样的算法可以用来让智能体适应完全不同、未知且复杂的条件。

强化学习有广泛的应用，通常用于无法通过经典方法解决的问题：

+   **游戏**。现有的方法可以设计最优的游戏策略，并超越人类。最著名的例子是国际象棋和围棋。

+   **机器人技术**。先进的算法可以集成到机器人中，帮助它们移动、搬运物品或完成家庭中的常规任务。

+   **自动驾驶**。强化学习方法可以开发用于…
