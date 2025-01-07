# PySpark 解析：`explode` 和 `collect_list` 函数

> 原文：[`towardsdatascience.com/pyspark-explained-the-explode-and-collect-list-functions-834f45ff5ac5?source=collection_archive---------11-----------------------#2024-06-18`](https://towardsdatascience.com/pyspark-explained-the-explode-and-collect-list-functions-834f45ff5ac5?source=collection_archive---------11-----------------------#2024-06-18)

![](img/fd0d72b6315cad72a4bb2149198bcb30.png)

图片来源：AI（Dalle-3）

## 两个在 PySpark 中嵌套和解嵌数据集的有用函数

[](https://medium.com/@thomas_reid?source=post_page---byline--834f45ff5ac5--------------------------------)![Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--834f45ff5ac5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--834f45ff5ac5--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--834f45ff5ac5--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--834f45ff5ac5--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--834f45ff5ac5--------------------------------) ·阅读时间：9 分钟·2024 年 6 月 18 日

--

PySpark SQL 是 Apache PySpark 中用于 SQL 的 Python 接口，是一个强大的数据转换和分析工具集。它旨在模拟数据库 SQL 系统中最常见的操作类型，此外，Pyspark SQL 还能够利用 Spark 中的 DataFrame 模式，提供额外的功能。

简而言之，Pyspark SQL 提供了一套丰富的函数，能够帮助开发人员高效地操作和处理数据。

在这些函数中，我想特别强调两个较为鲜为人知的函数，它们以独特的方式对数据进行转换和聚合，值得注意。这两个函数分别是 `explode` 和 `collect_list` 操作符。

在本文中，我将详细解释每个函数的作用，并展示一些使用案例以及每个函数的 PySpark 示例代码。

## Explode

在 PySpark SQL 中，`explode` 函数是一个多用途的工具，用于将嵌套的数据结构（如数组或映射）转换和扁平化为单独的行。当处理包含嵌套集合的复杂数据集时，这个函数特别有用，因为它允许你分析和操作单独的元素…
