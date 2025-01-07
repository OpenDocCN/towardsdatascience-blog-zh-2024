# 机器学习中的提升算法，第一部分：AdaBoost

> 原文：[https://towardsdatascience.com/boosting-algorithms-in-machine-learning-part-i-adaboost-b9d86041a521?source=collection_archive---------12-----------------------#2024-01-05](https://towardsdatascience.com/boosting-algorithms-in-machine-learning-part-i-adaboost-b9d86041a521?source=collection_archive---------12-----------------------#2024-01-05)

## 了解 AdaBoost 背后的逻辑，并使用 Python 实现它

[](https://medium.com/@gurjinderkaur95?source=post_page---byline--b9d86041a521--------------------------------)[![Gurjinder Kaur](../Images/d5c6746466025dad06077b1a89a789d1.png)](https://medium.com/@gurjinderkaur95?source=post_page---byline--b9d86041a521--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b9d86041a521--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b9d86041a521--------------------------------) [Gurjinder Kaur](https://medium.com/@gurjinderkaur95?source=post_page---byline--b9d86041a521--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b9d86041a521--------------------------------) ·13分钟阅读·2024年1月5日

--

![](../Images/3e3f0a714d392b74e007aec11aca3a71.png)

由 [Jeffrey Brandjes](https://unsplash.com/@jeffreyfotografie?utm_source=medium&utm_medium=referral) 拍摄，图片来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍

在机器学习中，提升（Boosting）是一种集成学习方法，它将多个弱学习器结合成一个强学习器。其思想是顺序地训练这些弱学习器，每个学习器尽力避免重复前一个学习器所犯的错误，最终构建出一个强大的集成模型。

在本文中，我们将学习一种流行的提升技术，称为 AdaBoost，并展示它如何优雅地使每个弱学习器将其错误传递给下一个弱学习器，从而最终提高预测质量。

本文将涵盖以下主题：

+   了解什么是***集成学习及其不同类型***。这就是我们将***定义*** ***提升算法***的地方。

+   了解什么是***弱学习器***，通过一个例子来说明，因为提升算法涉及到多个弱学习器的训练。

+   了解为什么***需要提升算法。***

+   ***AdaBoost 算法简介。***

+   在 Python 中***实现 AdaBoost 算法进行二分类***，并查看各个弱学习器的表现……
