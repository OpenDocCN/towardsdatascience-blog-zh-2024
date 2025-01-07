# 介绍 Python 的 datetime 模块

> 原文：[`towardsdatascience.com/introducing-pythons-datetime-module-13c5895b85a9?source=collection_archive---------4-----------------------#2024-05-14`](https://towardsdatascience.com/introducing-pythons-datetime-module-13c5895b85a9?source=collection_archive---------4-----------------------#2024-05-14)

## 快速成功的数据科学

## 初学者处理日期和时间的指南

[](https://medium.com/@lee_vaughan?source=post_page---byline--13c5895b85a9--------------------------------)![Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--13c5895b85a9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--13c5895b85a9--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--13c5895b85a9--------------------------------) [Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--13c5895b85a9--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--13c5895b85a9--------------------------------) ·10 分钟阅读·2024 年 5 月 14 日

--

![](img/d8a210178765943238055245bd0f1a02.png)

DALL-E3 制作的 Python 时钟

*时间序列*是按时间顺序索引的数据点序列。它们是科学和金融数据集中常见的组成部分，在这些数据集中，观察是在一段时间内进行的。

尽管你我都能识别“11/11/1918”作为一个日期，但计算机将其视为一个*文本字符串*。为了让 Python 能够智能地处理日历日期、小时、分钟、秒等信息，这些字符串必须转换为*特殊对象*，这些对象能“理解”公历、六十进制（基数 60）时间系统、时区、夏令时、闰年等时间规则。

原生 Python 通过其`[datetime](https://docs.python.org/3/library/datetime.html)`模块支持时间序列。在这篇*快速成功的数据科学*文章中，我将介绍如何使用`datetime`模块的基础知识。在随后的文章中，我会讲解如何利用 Python 的 pandas 数据分析库处理时间序列。

# Python 的`datetime`模块

Python 通过将时间信息视为特定的*数据类型*，可以正确而高效地处理时间。其内置的`datetime`模块包括`date`、`time`以及组合型`datetime`类型，允许处理时区、夏令时（DST）、闰年等不同的时间因素。
