# 利用数学规划和列生成方法求解资源规划问题

> 原文：[`towardsdatascience.com/solving-a-resource-planning-problem-with-mathematical-programming-and-column-generation-07fc7dd21ca6?source=collection_archive---------2-----------------------#2024-06-05`](https://towardsdatascience.com/solving-a-resource-planning-problem-with-mathematical-programming-and-column-generation-07fc7dd21ca6?source=collection_archive---------2-----------------------#2024-06-05)

## 使用 Python 和 Gurobipy 求解图着色问题的列生成方法

[](https://medium.com/@luisfernandopa1212?source=post_page---byline--07fc7dd21ca6--------------------------------)![Luis Fernando PÉREZ ARMAS, Ph.D.](https://medium.com/@luisfernandopa1212?source=post_page---byline--07fc7dd21ca6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--07fc7dd21ca6--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--07fc7dd21ca6--------------------------------) [Luis Fernando PÉREZ ARMAS, Ph.D.](https://medium.com/@luisfernandopa1212?source=post_page---byline--07fc7dd21ca6--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--07fc7dd21ca6--------------------------------) ·阅读时间：22 分钟·2024 年 6 月 5 日

--

![](img/21fc1345a2914c4942de9cd655a7697c.png)

毫无疑问，最好的资源规划者（图片由 DALLE-3 创建）

我曾在石油和天然气行业工作，我们面临的最棘手问题之一就是资源规划和预测。我和我的团队在这一方面常常遇到困难，经常是临时应对，不断扑灭火灾 🚒。我们仅仅是对局势做出反应，希望找到解决方案。但正如马克·沃尔伯格在电影中所说：“*希望不是一种战术*”。

> “但即兴发挥只能带你走到下一个危机的边缘，永远不能替代提前考虑几步并规划到最后的思维。”
> 
> **罗伯特·格林**，[**《权力的 48 条法则》**](https://www.goodreads.com/work/quotes/1297604)

我一直觉得必须有更好的方法来解决这个问题，但当时我并不知道 🤔。事实上，获得解决这些问题的技能是我继续学习的主要动力之一。本文探讨了数学优化的一个具体应用，以及它如何能让我的工作当时变得更加轻松。我想，我写这篇文章是送给过去的自己🎁。

我曾在一家提供钻探和测量服务的公司工作。该公司提供工具……
