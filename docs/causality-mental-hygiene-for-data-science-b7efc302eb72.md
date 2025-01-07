# 🧠🧹 因果性——数据科学的心理卫生

> 原文：[`towardsdatascience.com/causality-mental-hygiene-for-data-science-b7efc302eb72?source=collection_archive---------2-----------------------#2024-11-28`](https://towardsdatascience.com/causality-mental-hygiene-for-data-science-b7efc302eb72?source=collection_archive---------2-----------------------#2024-11-28)

## 利用因果工具激发*为什么*的力量。

[](https://eyal-kazin.medium.com/?source=post_page---byline--b7efc302eb72--------------------------------)![Eyal Kazin](https://eyal-kazin.medium.com/?source=post_page---byline--b7efc302eb72--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b7efc302eb72--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b7efc302eb72--------------------------------) [Eyal Kazin](https://eyal-kazin.medium.com/?source=post_page---byline--b7efc302eb72--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b7efc302eb72--------------------------------) ·37 分钟阅读·2024 年 11 月 28 日

--

![](img/3f42f9efb8d57d7c6417cb07dd8c3b95.png)

使用 Gemini Imagen 3 生成。除非另有说明，所有图片均由作者创作。

> 数据无法替代假设——犹太·佩尔（Judea Pearl）

应用与否，这是一个问题。

因果推理通过将焦点从*“发生了什么”*转向*“如果”*发生了什么，提升了预测结果。然而，在某些情况下，实施因果性可能具有挑战性，甚至是不可行的。本文探讨了**评估其适用性**这一行为本身的价值，因为它能提升你项目的科学严谨性。

这篇关于因果性的简明介绍的主要要点是：

+   无论是否适用，评估因果性适用性对心理卫生都有益。

+   通过应用因果推理，你将增强解决方案的科学严谨性，因为你将拥有表达问题的工具。

+   ***因果模型*** 是加深对机制理解的视觉辅助工具

+   ***可识别性*** 是评估特定因果问题适用性的框架

本文面向实践中的数据科学家、机器学习工程师、分析师及其他有兴趣通过因果推理进行决策的从业者。
