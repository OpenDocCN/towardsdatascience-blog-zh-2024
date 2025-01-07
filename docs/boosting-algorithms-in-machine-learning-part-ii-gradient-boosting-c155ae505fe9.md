# 机器学习中的提升算法，第二部分：梯度提升

> 原文：[https://towardsdatascience.com/boosting-algorithms-in-machine-learning-part-ii-gradient-boosting-c155ae505fe9?source=collection_archive---------7-----------------------#2024-11-12](https://towardsdatascience.com/boosting-algorithms-in-machine-learning-part-ii-gradient-boosting-c155ae505fe9?source=collection_archive---------7-----------------------#2024-11-12)

## 揭示一个简单却强大的、获奖的机器学习算法

[](https://medium.com/@gurjinderkaur95?source=post_page---byline--c155ae505fe9--------------------------------)[![Gurjinder Kaur](../Images/d5c6746466025dad06077b1a89a789d1.png)](https://medium.com/@gurjinderkaur95?source=post_page---byline--c155ae505fe9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c155ae505fe9--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c155ae505fe9--------------------------------) [Gurjinder Kaur](https://medium.com/@gurjinderkaur95?source=post_page---byline--c155ae505fe9--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c155ae505fe9--------------------------------) ·9分钟阅读·2024年11月12日

--

![](../Images/d9936a3e2b9a8b9c9e9776a9118b0af1.png)

图片来源：[Kevin Bowler](https://unsplash.com/@kdbowler?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在本文中，我们将学习梯度提升，一种机器学习算法，它为像XGBoost和LightGBM这样的流行框架奠定了基础，这些框架是多个机器学习竞赛的获奖解决方案。

这是一篇非常适合任何考虑在机器学习中使用集成方法的人阅读的文章，或者对于那些已经是专家但只是想暂时摆脱点拟合和点预测，想看看背后原理的人来说，也是一个很好的复习材料！

我们将介绍集成学习的基础知识，并通过一个逐步示例解释梯度提升算法如何进行预测。我们还将探索梯度下降和梯度提升之间的关系，看看它们是否存在联系。让我们开始吧！

# 集成学习

集成学习是训练并组合多个模型的过程，这些模型通常是弱学习器，目的是创造一个具有更高预测能力的强学习器。实现这一目标的两种方式是**bagging**和**boosting**。

## 1\. Bagging
