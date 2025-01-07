# Python 并发 —— 数据专业人员的脑力友好指南

> 原文：[`towardsdatascience.com/python-concurrency-a-brain-friendly-guide-for-data-professionals-a6215a3e9e26?source=collection_archive---------2-----------------------#2024-07-26`](https://towardsdatascience.com/python-concurrency-a-brain-friendly-guide-for-data-professionals-a6215a3e9e26?source=collection_archive---------2-----------------------#2024-07-26)

## 数据传输可能很慢。以下是如何从 Python 中挤压出每一丝性能优化。

[](https://medium.com/@radecicdario?source=post_page---byline--a6215a3e9e26--------------------------------)![Dario Radečić](https://medium.com/@radecicdario?source=post_page---byline--a6215a3e9e26--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a6215a3e9e26--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a6215a3e9e26--------------------------------) [Dario Radečić](https://medium.com/@radecicdario?source=post_page---byline--a6215a3e9e26--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a6215a3e9e26--------------------------------) ·阅读时长 9 分钟·2024 年 7 月 26 日

--

![](img/25762017e1f33ee69c80812c4b6e21c1.png)

图片来自 [Matthew Brodeur](https://unsplash.com/@mrbrodeur?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Python 经常被批评为最慢的编程语言之一。虽然这个说法有一定的依据，但必须指出，Python 通常是初学者学习的第一门编程语言。因此，大部分代码都非常低效。

但 Python 确实有一些小技巧。利用并发执行函数的方式实现起来非常简单，然而它能将数据处理管道的运行时间缩短十倍。**它将不再是几个小时，而是几分钟**。而且完全免费。

今天你将学习如何在 Python 中实现并发，并了解如何处理异常处理、自定义回调以及限流。让我们深入探讨吧！

# JSON Placeholder —— 今天的您的数据源

首要任务是配置数据源。我想避免使用一些专有的东西或需要花费大量时间设置的东西。[JSON Placeholder](http://jsonplaceholder.typicode.com) — 一个免费的 REST API 服务 — 是完美的选择。
