# 使用遗传算法解决旅行商问题

> 原文：[https://towardsdatascience.com/solving-the-travelling-salesman-problem-using-a-genetic-algorithm-c3e87f37f1de?source=collection_archive---------0-----------------------#2024-08-25](https://towardsdatascience.com/solving-the-travelling-salesman-problem-using-a-genetic-algorithm-c3e87f37f1de?source=collection_archive---------0-----------------------#2024-08-25)

## 使用Python的探索

[](https://medium.com/@jdhwilkins?source=post_page---byline--c3e87f37f1de--------------------------------)[![James Wilkins](../Images/fcdad070781b6e1d2cc2c293194a6f1a.png)](https://medium.com/@jdhwilkins?source=post_page---byline--c3e87f37f1de--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c3e87f37f1de--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c3e87f37f1de--------------------------------) [James Wilkins](https://medium.com/@jdhwilkins?source=post_page---byline--c3e87f37f1de--------------------------------)

·发布于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--c3e87f37f1de--------------------------------) ·阅读时间15分钟·2024年8月25日

--

你可以在[***这里***](https://github.com/jdhwilkins/Genetic-Algorithm-Experiments)查看此项目的笔记本***。***

![](../Images/7837887c8f6056581fa65614904827c5.png)

图片由DALL-E生成，OpenAI

## 旅行商问题

旅行商问题（**TSP**）描述了一个情境，其中一名销售员希望访问多个城市，并在返回起点之前采取最短的路线。虽然它看起来很简单，但这个问题不仅没有已知的多项式时间解法，而且没有有效的方法来证明某个给定的解是最优的。

相反，我们通常使用启发式算法来给出一个近似解，这对于许多实际应用来说已经足够。在本文中，我们将探索使用遗传算法生成“良好”解的不同方法。有关旅行商问题（TSP）难点的更深入讨论，以及一些用于解决它的启发式方法的总结，请查看[***这篇文章***](https://medium.com/@jdhwilkins/why-we-cant-completely-solve-the-travelling-salesman-problem-16b017d4a52a)。

## 遗传算法

遗传算法（**GA**）是一种模仿生物进化过程的机器学习技术。它是一种引导的随机搜索算法，可以非常高效地探索庞大的解空间。遗传算法涉及构建一个“染色体”种群，这些染色体作为潜在解的候选…
