# PySpark 解析：用户定义函数

> 原文：[`towardsdatascience.com/pyspark-explained-user-defined-functions-40a71f924b1a?source=collection_archive---------6-----------------------#2024-07-15`](https://towardsdatascience.com/pyspark-explained-user-defined-functions-40a71f924b1a?source=collection_archive---------6-----------------------#2024-07-15)

![](img/54123ff2074aa3399ee679449273534e.png)

图片由 AI（Dalle-3）生成

## 它们是什么？你又该如何使用它们呢？

[](https://medium.com/@thomas_reid?source=post_page---byline--40a71f924b1a--------------------------------)![Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--40a71f924b1a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--40a71f924b1a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--40a71f924b1a--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--40a71f924b1a--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--40a71f924b1a--------------------------------) ·阅读时间 10 分钟·2024 年 7 月 15 日

--

本文介绍的是 Spark 中的用户定义函数（UDF）。我将讲解它们是什么，如何使用它们，并通过在 PySpark 中编写的示例演示如何实现它们。

顺便提一下，当我谈论 PySpark 时，我指的是编写 Spark 程序时使用的底层语言是 Python。Spark 最初的开发语言是 ***Scala***，但随着 Python 的迅猛流行，现在即便 Spark 本身是用 Scala 编写的，Python 已成为人们在 Spark 中编程时主要使用的语言。

## 什么是 Spark？

如果你之前没有使用过 Spark 或者听说过 Spark，简而言之，它是一个用于快速处理和分析大量数据的强大工具。它是一个分布式计算引擎，旨在通过将任务拆分成较小的部分并并行处理它们来处理大数据任务。这使得它比许多其他方法更快、更高效，特别是在数据分析、机器学习和实时数据处理等复杂任务中。

现在作为 Apache 软件基金会的一部分，Spark 拥有多个关键组件，涵盖了数据处理和分析的不同方面，包括机器学习、SQL 操作和数据处理等…
