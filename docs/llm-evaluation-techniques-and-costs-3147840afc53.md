# LLM 评估技能很容易掌握（但实践起来成本高昂）

> 原文：[`towardsdatascience.com/llm-evaluation-techniques-and-costs-3147840afc53?source=collection_archive---------1-----------------------#2024-10-09`](https://towardsdatascience.com/llm-evaluation-techniques-and-costs-3147840afc53?source=collection_archive---------1-----------------------#2024-10-09)

## 这里是如何避免浪费预算在评估模型和系统上的方法

[](https://thuwarakesh.medium.com/?source=post_page---byline--3147840afc53--------------------------------)![Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--3147840afc53--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3147840afc53--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3147840afc53--------------------------------) [Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--3147840afc53--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3147840afc53--------------------------------) ·14 分钟阅读·2024 年 10 月 9 日

--

![](img/4a9ee96ef67aa94b0587a7004d3e0d1a.png)

图片由作者使用 Flux1.1 Pro 制作。

你可以通过两种方式建造一座堡垒：一种是开始将砖块一个个堆叠起来，另一种是先画出你即将建造的堡垒的图纸并规划执行方案；然后，按照你的计划不断评估它。

我们都知道第二种方法是我们*可能*构建堡垒的唯一途径。

有时候，我是我自己建议的最差追随者。我说的是直接跳入笔记本去构建 LLM 应用程序。这是我们破坏项目的最糟糕做法。

在我们开始之前，我们需要一个机制来告诉我们是否朝着正确的方向前进——也就是要说明我们上一次尝试的结果是否比之前更好（或者相反）。

在软件工程中，这叫做测试驱动开发。而对于机器学习来说，这就是评估。

> 开发基于 LLM 的应用程序的第一步，也是最宝贵的技能，就是定义你将如何评估你的项目。

评估 LLM 应用程序与软件测试完全不同。我并不是否定软件测试中的挑战，但评估 LLM 并不像测试那样简单直接。
