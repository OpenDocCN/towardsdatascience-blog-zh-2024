# 初学者的高级 ETL 技巧

> 原文：[`towardsdatascience.com/advanced-etl-techniques-for-beginners-03c404f0f0ac?source=collection_archive---------0-----------------------#2024-02-03`](https://towardsdatascience.com/advanced-etl-techniques-for-beginners-03c404f0f0ac?source=collection_archive---------0-----------------------#2024-02-03)

## 在 1 到 10 的范围内，你的数据摄取技能如何？

[](https://mshakhomirov.medium.com/?source=post_page---byline--03c404f0f0ac--------------------------------)![💡Mike Shakhomirov](https://mshakhomirov.medium.com/?source=post_page---byline--03c404f0f0ac--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--03c404f0f0ac--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--03c404f0f0ac--------------------------------) [💡Mike Shakhomirov](https://mshakhomirov.medium.com/?source=post_page---byline--03c404f0f0ac--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--03c404f0f0ac--------------------------------) ·14 分钟阅读·2024 年 2 月 3 日

--

![](img/93a1d86b6ee30e8f553af64221b5df98.png)

由[Blake Connally](https://unsplash.com/@blakeconnally?utm_source=medium&utm_medium=referral)拍摄，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

数据摄取是数据工程中的一个关键步骤。数据工程师将大量数据加载到各种数据库系统中，以便进行进一步的转换和处理。当处理相对较小的数据量时，我们很幸运不会遇到内存不足的问题，但在处理生产数据管道时，数据量达到 TB（甚至 PB）级别时，通常会变成一个真正的挑战。现有的 ETL 解决方案提供了将数据自动加载到数据仓库中的功能，并且通常采用按行计费的定价模式。在这个故事中，我想讨论如何为我们的数据管道创建一个定制的数据加载解决方案，以实现高效的数据加载。我们将深入了解常见的数据摄取设计模式和组织过程的典型方法。我们还将逆向工程一些最流行的 ETL 解决方案，看看如何高效地进行数据摄取而不会发生停机或数据丢失。我将提供使用 Python 库和市场上免费工具的数据加载示例，总结我的发现。

> 在 1 到 10 的范围内，你的数据加载技能如何？-

这是我在数据工程面试中最喜欢的一个问题之一。我一直在寻找人才…
