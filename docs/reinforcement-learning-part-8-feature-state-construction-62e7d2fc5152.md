# 强化学习，第8部分：特征状态构建

> 原文：[https://towardsdatascience.com/reinforcement-learning-part-8-feature-state-construction-62e7d2fc5152?source=collection_archive---------4-----------------------#2024-09-21](https://towardsdatascience.com/reinforcement-learning-part-8-feature-state-construction-62e7d2fc5152?source=collection_archive---------4-----------------------#2024-09-21)

## 通过智能地将状态特征融入学习目标来增强线性方法

[](https://medium.com/@slavahead?source=post_page---byline--62e7d2fc5152--------------------------------)[![Vyacheslav Efimov](../Images/441e600862b2b93564c6cd81abb0092d.png)](https://medium.com/@slavahead?source=post_page---byline--62e7d2fc5152--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--62e7d2fc5152--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--62e7d2fc5152--------------------------------) [Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--62e7d2fc5152--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--62e7d2fc5152--------------------------------) ·阅读时间13分钟·2024年9月21日

--

![](../Images/91aea78300be2957b1059721e4a216d5.png)

强化学习（**Reinforcement learning**）是机器学习的一个领域，引入了智能体在复杂环境中学习最优策略的概念。智能体通过其行动来学习，根据环境的状态产生奖励。强化学习是一个具有挑战性的话题，与机器学习的其他领域有显著不同。

强化学习的一个显著特点是，相同的算法可以用于使智能体适应完全不同、未知且复杂的环境条件。

# 关于本文

在 [第7部分](https://medium.com/towards-data-science/reinforcement-learning-part-7-introduction-to-value-function-approximation-2e22495f7008) 中，我们介绍了**价值函数近似**算法，这些算法扩展了标准的表格方法。除此之外，我们特别关注了一个非常重要的情况，即当近似的价值函数是**线性**时。正如我们所发现的，线性化提供了保证的收敛性，能够收敛到***全局最优解***或***TD固定点***（在***半梯度***方法中）。

问题在于，有时我们可能希望使用更复杂的近似价值函数，而不仅仅是一个简单的标量积，同时又不脱离线性优化空间。
