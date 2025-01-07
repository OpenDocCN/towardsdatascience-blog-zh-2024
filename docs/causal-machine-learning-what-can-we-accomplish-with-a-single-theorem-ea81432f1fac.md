# 因果机器学习：我们能通过一个定理实现什么？

> 原文：[`towardsdatascience.com/causal-machine-learning-what-can-we-accomplish-with-a-single-theorem-ea81432f1fac?source=collection_archive---------1-----------------------#2024-03-30`](https://towardsdatascience.com/causal-machine-learning-what-can-we-accomplish-with-a-single-theorem-ea81432f1fac?source=collection_archive---------1-----------------------#2024-03-30)

## 探索和利用双重机器学习背后看似简单的定理

[](https://harrisonfhoffman.medium.com/?source=post_page---byline--ea81432f1fac--------------------------------)![Harrison Hoffman](https://harrisonfhoffman.medium.com/?source=post_page---byline--ea81432f1fac--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ea81432f1fac--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ea81432f1fac--------------------------------) [Harrison Hoffman](https://harrisonfhoffman.medium.com/?source=post_page---byline--ea81432f1fac--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ea81432f1fac--------------------------------) ·27 分钟阅读·2024 年 3 月 30 日

--

![](img/887ac8bb1c54fd01f7c72ad08dc9c3a8.png)

北卡罗来纳州。图片由作者提供。

因果推断，特别是因果机器学习，是一项不可或缺的工具，能帮助我们通过理解因果关系来做出决策。优化定价、减少客户流失、运行定向广告活动以及决定哪些患者最能从医疗治疗中受益，都是因果机器学习的典型应用案例。

有许多技术可以用于因果机器学习问题，但最为突出的一种技术是双重机器学习（[DML](https://matheusfacure.github.io/python-causality-handbook/22-Debiased-Orthogonal-Machine-Learning.html)）或去偏/正交机器学习。除了 DML 的经验性成功外，这一技术之所以突出，还因为它拥有扎实的理论支持，源自计量经济学中的一个简单定理。

本文将通过实践示例解析支撑双重机器学习（DML）的定理。我们将讨论 DML 的直觉，并在越来越复杂的实例中经验性地验证其普适性。本文不是 DML 教程，而是作为动机，展示 DML 模型如何超越简单的相关性，理解和预测因果关系。

# 介于因果推断的（非常）简要入门
