# 数据工程师的 Pandas

> 原文：[https://towardsdatascience.com/pandas-for-data-engineers-a191965ac538?source=collection_archive---------0-----------------------#2024-02-10](https://towardsdatascience.com/pandas-for-data-engineers-a191965ac538?source=collection_archive---------0-----------------------#2024-02-10)

## 高级技巧：高效处理和加载数据

[](https://mshakhomirov.medium.com/?source=post_page---byline--a191965ac538--------------------------------)[![💡Mike Shakhomirov](../Images/bc6895c7face3244d488feb97ba0f68e.png)](https://mshakhomirov.medium.com/?source=post_page---byline--a191965ac538--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a191965ac538--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a191965ac538--------------------------------) [💡Mike Shakhomirov](https://mshakhomirov.medium.com/?source=post_page---byline--a191965ac538--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a191965ac538--------------------------------) ·阅读时间 9 分钟·2024年2月10日

--

![](../Images/fb218af19db1bade44b449337950a32c.png)

使用 [Kandinsky](https://github.com/ai-forever/Kandinsky-2) 生成的 AI 图像

在这篇文章中，我想谈谈我喜欢并且在编写 ETL 应用程序时经常使用 Pandas 的一些功能，用来处理数据。我们将讨论探索性数据分析、数据清洗和数据框变换。我将展示一些我最喜欢的技巧，以优化内存使用并高效处理大量数据。使用 Pandas 处理相对较小的数据集通常不会有问题。它能够轻松处理数据框中的数据，并提供一组非常方便的命令来处理这些数据。当涉及到更大数据框（1GB 及以上）上的数据变换时，我通常会使用 Spark 和分布式计算集群。Spark 能处理数 TB 和 PB 级的数据，但运行这些硬件可能会花费大量的资金。因此，当我们在内存资源有限的环境中处理中等大小的数据集时，Pandas 可能是一个更好的选择。

## Pandas 和 Python 生成器

在我之前的一篇文章中，我写到了如何使用 Python 中的生成器高效处理数据 [1]。
