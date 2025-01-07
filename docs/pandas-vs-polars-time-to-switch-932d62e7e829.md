# Pandas vs. Polars — 该是时候切换了吗？

> 原文：[https://towardsdatascience.com/pandas-vs-polars-time-to-switch-932d62e7e829?source=collection_archive---------0-----------------------#2024-04-07](https://towardsdatascience.com/pandas-vs-polars-time-to-switch-932d62e7e829?source=collection_archive---------0-----------------------#2024-04-07)

## 想要将数据处理管道的速度提高至10倍吗？也许是时候说再见给Pandas了。

[](https://medium.com/@radecicdario?source=post_page---byline--932d62e7e829--------------------------------)[![Dario Radečić](../Images/41882a3b30bab9da43d66a59f1df366b.png)](https://medium.com/@radecicdario?source=post_page---byline--932d62e7e829--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--932d62e7e829--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--932d62e7e829--------------------------------) [Dario Radečić](https://medium.com/@radecicdario?source=post_page---byline--932d62e7e829--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--932d62e7e829--------------------------------) ·阅读时间7分钟·2024年4月7日

--

![](../Images/af0f664cff90facab29561f0bb822acf.png)

图片来源：[Hans-Jurgen Mager](https://unsplash.com/@hansjurgen007?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在一个按秒计费的计算世界里，尽可能地将计算时间最小化是合乎逻辑的。甚至要更进一步。

Python庞大的数据处理生态系统对于初学者来说非常友好，但随着数据集的增大，扩展变得十分困难。**并行处理**、**查询优化**和**延迟计算**在Pandas中是闻所未闻的概念，但如果你想在大规模生产环境中使用Python，这些概念是必须理解的。

介绍[Polars](https://pola.rs/)。它是一个从头开始编写的Python库，专注于性能优化。Polars具有一个用Rust编写的多线程查询引擎，这意味着你可以期待看到比Pandas快得多的数据处理速度，甚至[比Pandas快30到50倍](https://github.com/pola-rs/tpch)。

今天，你将看到如何通过一系列4个基准测试，比较Polars与Pandas在处理一个有1100万行的CSV文件时的表现。

但首先，让我们来回顾一下为什么你应该考虑将Polars作为Pandas的替代品。

# Pandas vs. Polars — 为什么作为数据专业人士你应该考虑Polars
