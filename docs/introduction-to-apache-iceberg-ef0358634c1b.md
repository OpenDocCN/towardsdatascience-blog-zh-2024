# Apache Iceberg 简介

> 原文：[`towardsdatascience.com/introduction-to-apache-iceberg-ef0358634c1b?source=collection_archive---------2-----------------------#2024-02-29`](https://towardsdatascience.com/introduction-to-apache-iceberg-ef0358634c1b?source=collection_archive---------2-----------------------#2024-02-29)

## 探讨 Apache Iceberg 的优缺点，以及它如何帮助构建你自己的 Lakehouse。

[](https://pierpaoloippolito28.medium.com/?source=post_page---byline--ef0358634c1b--------------------------------)![Pier Paolo Ippolito](https://pierpaoloippolito28.medium.com/?source=post_page---byline--ef0358634c1b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ef0358634c1b--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ef0358634c1b--------------------------------) [Pier Paolo Ippolito](https://pierpaoloippolito28.medium.com/?source=post_page---byline--ef0358634c1b--------------------------------)

·发表于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--ef0358634c1b--------------------------------) ·5 分钟阅读·2024 年 2 月 29 日

--

![](img/ba3ce7adcfced4fd24b038de63d667e8.png)

图片来源：[Alexander Hafemann](https://unsplash.com/@mlenny?utm_source=medium&utm_medium=referral) 在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍

随着 GCP、Azure 和 AWS 等云服务提供商使数据湖（Data Lakes）变得易于访问，越来越多的组织能够廉价地存储其非结构化数据。尽管数据湖有许多限制，例如：

+   在混合批处理和流式处理或附加新数据时，可能会出现不一致的读取情况。

+   对现有数据的细粒度修改可能变得复杂（例如，为了满足 GDPR 要求）

+   在处理数百万个小文件时性能下降。

+   不支持 ACID（原子性、一致性、隔离性、持久性）事务。

+   没有模式强制执行/演化。

为了尝试缓解这些问题，Apache Iceberg 由 Netflix 于 2017 年提出。Apache Iceberg 是一种表格格式，能够为支持 ACID 事务、时间旅行等提供额外的抽象层，同时与各种数据源和工作负载一起使用。表格格式的主要目标是定义一种协议，指导如何最好地管理和组织构成表格的所有文件。除了 Apache Iceberg，还有其他...
