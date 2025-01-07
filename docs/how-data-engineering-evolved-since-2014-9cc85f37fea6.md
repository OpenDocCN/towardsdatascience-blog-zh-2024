# 数据工程自2014年以来的演变

> 原文：[https://towardsdatascience.com/how-data-engineering-evolved-since-2014-9cc85f37fea6?source=collection_archive---------0-----------------------#2024-07-27](https://towardsdatascience.com/how-data-engineering-evolved-since-2014-9cc85f37fea6?source=collection_archive---------0-----------------------#2024-07-27)

## 帮助数据管道轻松扩展的主要趋势

[](https://mshakhomirov.medium.com/?source=post_page---byline--9cc85f37fea6--------------------------------)[![💡Mike Shakhomirov](../Images/bc6895c7face3244d488feb97ba0f68e.png)](https://mshakhomirov.medium.com/?source=post_page---byline--9cc85f37fea6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9cc85f37fea6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9cc85f37fea6--------------------------------) [💡Mike Shakhomirov](https://mshakhomirov.medium.com/?source=post_page---byline--9cc85f37fea6--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9cc85f37fea6--------------------------------) ·阅读时间：13分钟·2024年7月27日

--

![](../Images/e898a0993dfde1016916adf84eccc707.png)

使用 AI 生成的图像，来源于[Kandinsky](https://github.com/ai-forever/Kandinsky-2)

在这次讨论中，我旨在探讨数据编排和数据建模中的不断发展趋势，重点介绍工具的进步及其对数据工程师的核心好处。尽管自2014年以来，Airflow 一直是主导者，但数据工程的格局已经发生了显著变化，现在能够应对更复杂的用例和需求，包括支持多种编程语言、集成和增强的可扩展性。我将考察一些当代的、或许是非传统的工具，这些工具简化了我的数据工程流程，使我能够轻松创建、管理和编排强大、耐用且可扩展的数据管道。

在过去的十年里，我们见证了各种用于数据提取、转换和编排的 ETL 框架的“寒武纪大爆发”。不难发现，其中许多框架是开源的，并且基于 Python。

最流行的框架：

+   Airflow，2014年

+   Luigi，2014年

+   Prefect，2018年

+   Temporal，2019年

+   Flyte，2020年

+   Dagster，2020年

+   Mage，2021年

+   Orchestra，2023年
