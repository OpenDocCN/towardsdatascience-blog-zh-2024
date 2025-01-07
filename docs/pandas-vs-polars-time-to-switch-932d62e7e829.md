# Pandas vs. Polars — 该是时候切换了吗？

> 原文：[`towardsdatascience.com/pandas-vs-polars-time-to-switch-932d62e7e829?source=collection_archive---------0-----------------------#2024-04-07`](https://towardsdatascience.com/pandas-vs-polars-time-to-switch-932d62e7e829?source=collection_archive---------0-----------------------#2024-04-07)

## 想要将数据处理管道的速度提高至 10 倍吗？也许是时候说再见给 Pandas 了。

[](https://medium.com/@radecicdario?source=post_page---byline--932d62e7e829--------------------------------)![Dario Radečić](https://medium.com/@radecicdario?source=post_page---byline--932d62e7e829--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--932d62e7e829--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--932d62e7e829--------------------------------) [Dario Radečić](https://medium.com/@radecicdario?source=post_page---byline--932d62e7e829--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--932d62e7e829--------------------------------) ·阅读时间 7 分钟·2024 年 4 月 7 日

--

![](img/af0f664cff90facab29561f0bb822acf.png)

图片来源：[Hans-Jurgen Mager](https://unsplash.com/@hansjurgen007?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在一个按秒计费的计算世界里，尽可能地将计算时间最小化是合乎逻辑的。甚至要更进一步。

Python 庞大的数据处理生态系统对于初学者来说非常友好，但随着数据集的增大，扩展变得十分困难。**并行处理**、**查询优化**和**延迟计算**在 Pandas 中是闻所未闻的概念，但如果你想在大规模生产环境中使用 Python，这些概念是必须理解的。

介绍[Polars](https://pola.rs/)。它是一个从头开始编写的 Python 库，专注于性能优化。Polars 具有一个用 Rust 编写的多线程查询引擎，这意味着你可以期待看到比 Pandas 快得多的数据处理速度，甚至[比 Pandas 快 30 到 50 倍](https://github.com/pola-rs/tpch)。

今天，你将看到如何通过一系列 4 个基准测试，比较 Polars 与 Pandas 在处理一个有 1100 万行的 CSV 文件时的表现。

但首先，让我们来回顾一下为什么你应该考虑将 Polars 作为 Pandas 的替代品。

# Pandas vs. Polars — 为什么作为数据专业人士你应该考虑 Polars
