# 假设检验解释（我希望它是这样向我解释的）

> 原文：[`towardsdatascience.com/hypothesis-testing-explained-how-i-wish-it-was-explained-to-me-14379f7a41c3?source=collection_archive---------1-----------------------#2024-05-13`](https://towardsdatascience.com/hypothesis-testing-explained-how-i-wish-it-was-explained-to-me-14379f7a41c3?source=collection_archive---------1-----------------------#2024-05-13)

## 大多数资源关注的是置信度和功效之类的东西。但这些其实并不重要：以下是你应该关心的内容

[](https://medium.com/@mazzanti.sam?source=post_page---byline--14379f7a41c3--------------------------------)![Samuele Mazzanti](https://medium.com/@mazzanti.sam?source=post_page---byline--14379f7a41c3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--14379f7a41c3--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--14379f7a41c3--------------------------------) [Samuele Mazzanti](https://medium.com/@mazzanti.sam?source=post_page---byline--14379f7a41c3--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--14379f7a41c3--------------------------------) ·阅读时间 8 分钟·2024 年 5 月 13 日

--

![](img/383a6f28bdbdf3a6695caf7e3e6f87e7.png)

[图片由作者提供]

我第一次学习假设检验是在我统计学本科学习的第一年。从那时起，我就一直觉得**我在某些方面有些缺失**。

让我特别困扰的是那些看起来**相当任意**的元素，比如那些“魔法数字”，例如 80%的功效或 97.5%的置信度。

所以我最近尝试深入研究这个话题，并在某个时刻，我有了那种*顿悟*的瞬间，一切都立刻清晰起来。

这就是我写这篇文章的原因：为了以我希望别人最初能向我解释的方式解释假设检验。并且希望能**让你也达到我曾有过的*顿悟*时刻**。

本文将按照以下段落结构展开：

+   第 0 步：查看混淆矩阵

+   第 1 步：定义假设

+   第 2 步：评估假设的现实性

+   第 3 步：填充混淆矩阵

+   第 4 步：计算样本维度
