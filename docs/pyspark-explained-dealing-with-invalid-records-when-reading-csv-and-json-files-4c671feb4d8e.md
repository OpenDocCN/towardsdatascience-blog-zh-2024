# PySpark 解析：处理读取 CSV 和 JSON 文件时的无效记录

> 原文：[`towardsdatascience.com/pyspark-explained-dealing-with-invalid-records-when-reading-csv-and-json-files-4c671feb4d8e?source=collection_archive---------8-----------------------#2024-06-25`](https://towardsdatascience.com/pyspark-explained-dealing-with-invalid-records-when-reading-csv-and-json-files-4c671feb4d8e?source=collection_archive---------8-----------------------#2024-06-25)

![](img/773bc225167a3d85a50cc3f7756207c5.png)

图片来源：AI（Ideogram）

## 有效的技术来识别和处理数据错误

[](https://medium.com/@thomas_reid?source=post_page---byline--4c671feb4d8e--------------------------------)![Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--4c671feb4d8e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4c671feb4d8e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4c671feb4d8e--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--4c671feb4d8e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4c671feb4d8e--------------------------------) ·8 分钟阅读·2024 年 6 月 25 日

--

如果你是 PySpark 的常用用户，那么你最常做的操作之一就是将 CSV 或 JSON 数据从外部文件读取到 DataFrame 中。如果你的输入数据有一个用户指定的模式定义，你可能会发现，并非所有处理的记录都符合该模式的规范。

换句话说，可能存在无效记录。也许一些字段缺失，或者存在一些未被考虑的多余字段，或者某些字段的数据类型与模式中指定的不符。对于小文件来说，追踪这些记录并不困难，但对于大数据世界中的庞大文件，这些问题可能会成为一个真正的头痛问题。问题是，在这些情况下我们该怎么办？

正如你很快会发现的那样，解决这个问题的一个方法是使用在将 CSV 或 JSON 文件读取到 DataFrame 时，PySpark 提供的各种`parse`选项。

## 访问免费的 PySpark 开发环境

在我们继续之前，如果你想跟随本文中的代码进行操作，你需要访问一个 PySpark 开发环境。
