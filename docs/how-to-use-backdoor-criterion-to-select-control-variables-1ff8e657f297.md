# 如何使用反门准则选择控制变量

> 原文：[`towardsdatascience.com/how-to-use-backdoor-criterion-to-select-control-variables-1ff8e657f297?source=collection_archive---------13-----------------------#2024-01-09`](https://towardsdatascience.com/how-to-use-backdoor-criterion-to-select-control-variables-1ff8e657f297?source=collection_archive---------13-----------------------#2024-01-09)

## 通过 R 中的模拟实验进行说明

[](https://weisharon88.medium.com/?source=post_page---byline--1ff8e657f297--------------------------------)![Shuangyuan (Sharon) Wei](https://weisharon88.medium.com/?source=post_page---byline--1ff8e657f297--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1ff8e657f297--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1ff8e657f297--------------------------------) [Shuangyuan (Sharon) Wei](https://weisharon88.medium.com/?source=post_page---byline--1ff8e657f297--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1ff8e657f297--------------------------------) ·6 分钟阅读·2024 年 1 月 9 日

--

![](img/4dbfbfe3c3803c2c9ae140b593aede50.png)

图片由 [Katerina Pavlyuchkova](https://unsplash.com/@kat_katerina?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来自 [Unsplash](https://unsplash.com/photos/opened-brown-wooden-window-FQYCJSqER_0?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

# 引言

在这篇文章中，我将解释如何在实验环境中使用反门准则（backdoor criterion）来选择好的控制变量，或避免选择不好的控制变量，使用的是有向无环图（DAG）。我通过潜在结果模型开始了自己的因果推断之旅，该模型在我之前的[文章](https://medium.com/geekculture/an-introduction-of-randomized-experiment-aka-a-b-testing-and-potential-outcome-model-f2c93f73d426)中有所介绍。我最近通过参加 Jason Roos 教授的精彩实验与因果推断课程，才“发现”了 DAG，并且非常喜欢 DAG 作为一个框架，能够轻松地构建理论并可视化因果模型。它通过使模型中包含的变量以及这些变量之间关系的假设变得显而易见，从而促进了识别分析。因此，它也有助于识别混杂变量并分析如何消除混杂。

我假设读者已经了解 DAG 的基础知识（如果没有的话，Scott Cunningham 的因果推断[Mixtape](https://mixtape.scunning.com/03-directed_acyclical_graphs)是一个有帮助的起点），并且我相信掌握后门准则的最快方法是通过例子。因此，我将按以下方式进行：首先，我将提出我们想要回答的问题，并提供 DAG 的表示，以便我们能轻松地概念化它；接下来，我将解释什么是后门…
