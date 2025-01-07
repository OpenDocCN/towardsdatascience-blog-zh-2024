# 布隆过滤器的奇妙之处：实用指南

> 原文：[`towardsdatascience.com/the-wonders-of-bloom-filters-a-practical-guide-b4e3845656b4?source=collection_archive---------4-----------------------#2024-08-10`](https://towardsdatascience.com/the-wonders-of-bloom-filters-a-practical-guide-b4e3845656b4?source=collection_archive---------4-----------------------#2024-08-10)

## 在本文中，我们将探讨布隆过滤器以及它们如何大大提升应用性能。了解这种巧妙的数据结构如何显著减少内存使用并加速大数据集的查询！

[](https://satyaganesh.medium.com/?source=post_page---byline--b4e3845656b4--------------------------------)![Kuruva Satya Ganesh](https://satyaganesh.medium.com/?source=post_page---byline--b4e3845656b4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b4e3845656b4--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b4e3845656b4--------------------------------) [Kuruva Satya Ganesh](https://satyaganesh.medium.com/?source=post_page---byline--b4e3845656b4--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b4e3845656b4--------------------------------) ·阅读时间 8 分钟 ·2024 年 8 月 10 日

--

![](img/2a8ae051f9988dc524923298eef33cbc.png)

图片由 [Nathan Dumlao](https://unsplash.com/@nate_dumlao?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在班加罗尔的一家热闹的咖啡馆里，Perry 和 Alex 手捧冰咖啡，围坐在笔记本电脑前，准备解决他们最新的数据管理挑战。

***Perry:*** “我们在数据库查询方面遇到了一个重大问题。我们每天大约收到 ***1000 万次请求***，但不幸的是，大约 **40%** 的查询都返回了‘未找到数据’。查询那些几乎总是不存在的记录，感觉是巨大的资源浪费。”

***Alex:*** “那样效率会很低。你有没有考虑过一种数据结构，可以在执行查询之前，快速检查某个项目是否可能存在于数据库中？”

***Perry:*** “我在考虑哈希表，它们提供快速查找，因为在平均情况下，它们对搜索的时间复杂度是常数。考虑到数据量，似乎是个不错的选择。”

***Alex:*** “哈希表确实提供快速查找，但它们会消耗大量内存，例如，如果你有…
