# 介绍n步时间差分方法

> 原文：[https://towardsdatascience.com/introducing-n-step-temporal-difference-methods-7f7878b3441c?source=collection_archive---------1-----------------------#2024-12-29](https://towardsdatascience.com/introducing-n-step-temporal-difference-methods-7f7878b3441c?source=collection_archive---------1-----------------------#2024-12-29)

## 剖析理查德·S·萨顿的《强化学习》，附带定制的Python实现，第五集

[](https://medium.com/@hrmnmichaels?source=post_page---byline--7f7878b3441c--------------------------------)[![Oliver S](../Images/b5ee0fa2d5fb115f62e2e9dfcb92afdd.png)](https://medium.com/@hrmnmichaels?source=post_page---byline--7f7878b3441c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7f7878b3441c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7f7878b3441c--------------------------------) [Oliver S](https://medium.com/@hrmnmichaels?source=post_page---byline--7f7878b3441c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7f7878b3441c--------------------------------) ·阅读时间10分钟·6天前

--

在我们之前的文章中，我们通过探索时间差分（TD）学习总结了强化学习（RL）技术的基础系列。TD方法结合了动态规划（DP）和蒙特卡罗（MC）方法的优点，利用它们的最佳特性形成了一些最重要的RL算法，如Q学习。

基于这一基础，本文深入探讨了**n步TD学习**，这是萨顿在其书籍第7章中介绍的一个多功能方法[1]。该方法弥合了经典TD和MC技术之间的差距。像TD一样，n步方法使用自举（利用先前的估计），但它们还结合了接下来的`n`个奖励，提供了短期和长期学习的独特结合。在未来的文章中，我们将通过**资格迹**进一步概括这一概念。

我们将采用一种结构化方法，从**预测问题**开始，然后转向**控制**。在这个过程中，我们将：

+   介绍**n步Sarsa**，

+   将其扩展到**离策略学习**，

+   探索**n步树备份算法**，并

+   提出一个统一的视角，使用**n步Q(σ)**。

像往常一样，您可以在[GitHub](https://github.com/hermanmichaels/rl_book)上找到所有配套代码。让我们开始吧！
