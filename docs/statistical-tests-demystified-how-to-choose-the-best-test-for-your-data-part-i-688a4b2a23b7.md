# 掌握统计检验（第一部分）

> 原文：[https://towardsdatascience.com/statistical-tests-demystified-how-to-choose-the-best-test-for-your-data-part-i-688a4b2a23b7?source=collection_archive---------4-----------------------#2024-05-19](https://towardsdatascience.com/statistical-tests-demystified-how-to-choose-the-best-test-for-your-data-part-i-688a4b2a23b7?source=collection_archive---------4-----------------------#2024-05-19)

## 选择适合您数据的正确检验的指南

[](https://medium.com/@shreef.nasser?source=post_page---byline--688a4b2a23b7--------------------------------)[![Sheref Nasereldin, Ph.D.](../Images/91c0848fe85633bec0c0004004a64a62.png)](https://medium.com/@shreef.nasser?source=post_page---byline--688a4b2a23b7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--688a4b2a23b7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--688a4b2a23b7--------------------------------) [Sheref Nasereldin, Ph.D.](https://medium.com/@shreef.nasser?source=post_page---byline--688a4b2a23b7--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--688a4b2a23b7--------------------------------) ·12分钟阅读·2024年5月19日

--

![](../Images/67d3591de2c53f26624c6f96eac9c57c.png)

图片来源：[Adam Nowakowski](https://unsplash.com/@adamaszczos?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

你是否曾经遇到过一个数据集，结果发现自己对于哪个统计显著性检验最适合回答你的研究问题感到迷茫和困惑？放心，告诉你，你并不孤单。我曾经也是那个人！尽管我尊重统计学，但我并没有对它产生强烈的热情。在这篇文章中，我将重点讲解一些关键概念，帮助你在选择合适的统计显著性检验时做出明智的决定。由于进行统计显著性检验本质上涉及处理变量（自变量和因变量），因此我认为有必要先了解不同类型的这些变量。

# **数据类型：**

![](../Images/0de53389db176c47b8921eed40d6cafe.png)

图片来源：[Claudio Schwarz](https://unsplash.com/@purzlbaum?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

**1- 分类或名义变量**

分类变量（或名义变量）有两个或更多没有内在顺序的类别。例如，眼睛颜色是一个分类变量，其类别包括蓝色、绿色、棕色和榛色。对于这些类别没有公认的排名方式。如果一个变量有明确的顺序，那么它就是一个顺序变量，下面会讨论这一点。
