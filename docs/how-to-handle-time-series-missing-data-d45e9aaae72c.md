# 如何处理时间序列缺失数据

> 原文：[`towardsdatascience.com/how-to-handle-time-series-missing-data-d45e9aaae72c?source=collection_archive---------1-----------------------#2024-02-01`](https://towardsdatascience.com/how-to-handle-time-series-missing-data-d45e9aaae72c?source=collection_archive---------1-----------------------#2024-02-01)

## 缺失值的原因及如何使用 Python 处理它们

[](https://vcerq.medium.com/?source=post_page---byline--d45e9aaae72c--------------------------------)![Vitor Cerqueira](https://vcerq.medium.com/?source=post_page---byline--d45e9aaae72c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d45e9aaae72c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d45e9aaae72c--------------------------------) [Vitor Cerqueira](https://vcerq.medium.com/?source=post_page---byline--d45e9aaae72c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d45e9aaae72c--------------------------------) ·6 分钟阅读·2024 年 2 月 1 日

--

![](img/d898f76296bb6d5df74eb633c4e54c0a.png)

图片来自 [Anton Nazaretian](https://unsplash.com/@anton_nazaretian?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍

数据收集中的问题可能导致缺失数据。这个问题可能由于各种原因引起，例如传感器维护或传输故障。

缺失数据通常通过数据插补策略来解决，例如用中心统计值替代缺失值。对于时间序列，插补过程更具挑战性，因为观测值是有序的。除此之外，选择一个考虑到缺失数据机制的策略也可能很有用。

在本文中，你将了解时间序列缺失数据的主要模式，以及如何处理它们。

# **缺失数据的原因及其重要性**

缺失数据的模式，如其频率，取决于导致数据缺失的机制。

一般来说，缺失数据的原因可以归为以下几类：

+   完全随机缺失：当没有系统性过程导致观测数据缺失时。也就是说，缺失的数据与 1）观测值和 2）过去或未来的观测值之间没有关系，无论这些值是否也缺失…
