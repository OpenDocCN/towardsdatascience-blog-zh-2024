# 偏倚与方差如何影响你的模型

> 原文：[`towardsdatascience.com/how-bias-and-variance-affect-your-model-a03b1c3dd6d1?source=collection_archive---------6-----------------------#2024-12-24`](https://towardsdatascience.com/how-bias-and-variance-affect-your-model-a03b1c3dd6d1?source=collection_archive---------6-----------------------#2024-12-24)

## 学习概念和实践。了解模型在每种情况下的表现。

[](https://gustavorsantos.medium.com/?source=post_page---byline--a03b1c3dd6d1--------------------------------)![Gustavo R Santos](https://gustavorsantos.medium.com/?source=post_page---byline--a03b1c3dd6d1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a03b1c3dd6d1--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a03b1c3dd6d1--------------------------------) [Gustavo R Santos](https://gustavorsantos.medium.com/?source=post_page---byline--a03b1c3dd6d1--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a03b1c3dd6d1--------------------------------) ·阅读时间 7 分钟·2024 年 12 月 24 日

--

![](img/29fa767247b39410915d7550e2038134.png)

击中靶心 | Google Gemini 2.0 Flash，2024\. https://gemini.google.com

# 介绍

自从我开始转向数据科学以来，我听说过著名的 *偏倚与方差权衡*。

但我学到了足够的知识，以便继续我的研究，并且没有过多回头看。我一直知道，高度偏倚的模型会导致数据欠拟合，而高变异的模型会导致过拟合，这两者在训练机器学习模型时都不是好选择。

我也知道，我们应该在这两种状态之间寻找平衡，这样我们就能得到 *良好的拟合* 或一个能够将模式很好地泛化到新数据的模型。

但我可能会说，我从未走得更远。我从未搜索或创建高度偏倚或高度变异的模型，仅仅是为了看看它们对数据的实际影响，以及这些模型的预测结果如何。

当然，直到今天，因为这正是我们在这篇文章中所做的。让我们继续进行一些定义。

# 高偏倚

![](img/a67a940074637324905fe5aec8017a6a.png)

过度简化 = 用锤子敲打任何东西 | Google Gemini，2024\. [`gemini.google.com`](https://gemini.google.com)
