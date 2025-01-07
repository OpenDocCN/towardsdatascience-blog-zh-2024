# Pandas 中的 TSV：操作指南

> 原文：[`towardsdatascience.com/tsv-in-pandas-a-how-to-guide-1854b4b86c7f?source=collection_archive---------17-----------------------#2024-10-01`](https://towardsdatascience.com/tsv-in-pandas-a-how-to-guide-1854b4b86c7f?source=collection_archive---------17-----------------------#2024-10-01)

## 使用 Pandas 正确加载和写入 TSV 文件的方法

[](https://czuk.medium.com/?source=post_page---byline--1854b4b86c7f--------------------------------)![Michał Marcińczuk, Ph.D.](https://czuk.medium.com/?source=post_page---byline--1854b4b86c7f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1854b4b86c7f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1854b4b86c7f--------------------------------) [Michał Marcińczuk, Ph.D.](https://czuk.medium.com/?source=post_page---byline--1854b4b86c7f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1854b4b86c7f--------------------------------) ·阅读时长 5 分钟·2024 年 10 月 1 日

--

![](img/c9fa95b74cc41a1b9df210c30f961f96.png)

图片由[Mika Baumeister](https://unsplash.com/@kommumikation?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍

TSV 是一种广泛使用的存储表格数据的格式，但在处理文本数据和使用 Pandas 库时，可能会引起混淆。混淆的原因有两个：

1.  TSV 与 CSV（广泛使用的存储数据格式）非常相似，但并不完全相同。

1.  Pandas 的默认设置与 TSV 格式不兼容。

在本文中，我简要讨论了产生混淆的原因，并展示了使用 Pandas 库处理 TSV 格式的最佳方法。

# TSV 格式

TSV[1]是一种简单的文件格式，类似于 CSV。然而，它有几个重要的区别：

1.  它使用制表符来分隔字段。

1.  它不允许某些字符，即**换行符**（`\n`）、**制表符**（`\t`）和**回车符**（`\r`）出现在字段内。

1.  字段没有引用，也没有特殊字符的转义[2]（至少对于原始格式而言）。

第 2 点在处理文本字段时是一个问题，因为它们可能包含禁用字符。建议的处理禁用字符的方法是将它们替换为任意文本，例如……
