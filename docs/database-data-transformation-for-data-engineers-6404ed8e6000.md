# 数据库数据转换（面向数据工程师）

> 原文：[https://towardsdatascience.com/database-data-transformation-for-data-engineers-6404ed8e6000?source=collection_archive---------1-----------------------#2024-02-17](https://towardsdatascience.com/database-data-transformation-for-data-engineers-6404ed8e6000?source=collection_archive---------1-----------------------#2024-02-17)

## 初学者的高级技术

[](https://mshakhomirov.medium.com/?source=post_page---byline--6404ed8e6000--------------------------------)[![💡Mike Shakhomirov](../Images/bc6895c7face3244d488feb97ba0f68e.png)](https://mshakhomirov.medium.com/?source=post_page---byline--6404ed8e6000--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6404ed8e6000--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6404ed8e6000--------------------------------) [💡Mike Shakhomirov](https://mshakhomirov.medium.com/?source=post_page---byline--6404ed8e6000--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6404ed8e6000--------------------------------) ·14分钟阅读·2024年2月17日

--

![](../Images/cf75ce2871beb0466eab1abc43baaa20.png)

使用 [Kandinsky](https://github.com/ai-forever/Kandinsky-2) 生成的AI图像

在这个故事中，我想引发一个关于如何进行数据转换的讨论。无论是数据库、数据仓库还是报告解决方案，我们都基于数据模型执行数据转换，但我们如何组织它们呢？我想谈谈你使用的现代数据转换工具。我们将涉及模块化方法、调度和数据转换测试的一些细节。在本文的结尾，我将提供一个示例应用，用于执行数据建模任务，并具备数据血缘和自文档化功能。我非常想知道你对此的看法。

我见证了许多不同方式的数据转换。在我超过十五年的大数据和分析领域职业生涯中，我构建了多种不同设计模式的数据管道，我相信还有更多方式。这就是我如此喜欢技术世界的原因。它提供的无限可能性简直令人惊叹。

> 你使用什么操作系统来管理你的数据仓库？

## 现代数据转换工具

现代数据转换工具，也称为数据建模工具或数据仓库（DWH）操作系统，旨在简化SQL数据操作任务，以...
