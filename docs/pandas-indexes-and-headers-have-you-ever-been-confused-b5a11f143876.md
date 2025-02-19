# Pandas 索引和标题，你曾感到困惑过吗？

> 原文：[`towardsdatascience.com/pandas-indexes-and-headers-have-you-ever-been-confused-b5a11f143876?source=collection_archive---------3-----------------------#2024-06-09`](https://towardsdatascience.com/pandas-indexes-and-headers-have-you-ever-been-confused-b5a11f143876?source=collection_archive---------3-----------------------#2024-06-09)

![](img/fb42f7211fb184a74ced1d942e3b64f8.png)

由作者在 Canva 中创建

## 从单级索引和标题到多级索引和标题，为什么以及如何实现？

[](https://christophertao.medium.com/?source=post_page---byline--b5a11f143876--------------------------------)![Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--b5a11f143876--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b5a11f143876--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b5a11f143876--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--b5a11f143876--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b5a11f143876--------------------------------) ·阅读时间：9 分钟·2024 年 6 月 9 日

--

有很多关于 Pandas 的教程，但大多数都在试图告诉我们一些技巧。我仍然记得当我刚接触 Python Pandas 库时，我曾对索引和标题感到困惑，尤其是在它们有多个级别的时候。

在这篇文章中，我将重点介绍与索引和标题相关的概念，以及 Pandas 数据框的重塑操作。我知道多级索引和多级标题是最让人困惑的概念，因此我将尽力通过许多示例在一个部分中进行解释。希望这篇文章能帮助你理解这些概念！

# 1\. 基础

![](img/b57240bfc76e07527cb70d9b44e07b11.png)

由作者在 Canva 中创建

让我们从创建一个简单的数据框开始，我们需要它来展示后面的示例。如果你是 Pandas 的新手，这也是一个很好的热身机会。

## 1.1 创建带有标题的数据框

```py
import pandas as pd

data = {
    'Name': ['Alice', 'Bob', 'Chris'],
    'Age': [25, 30, 35]
}…
```
