# 系统设计：一致性哈希

> 原文：[`towardsdatascience.com/system-design-consistent-hashing-43ddf48d2d32?source=collection_archive---------1-----------------------#2024-03-13`](https://towardsdatascience.com/system-design-consistent-hashing-43ddf48d2d32?source=collection_archive---------1-----------------------#2024-03-13)

## 解锁分布式数据库中高效数据分区的力量，如 Cassandra 和 Dynamo DB。

[](https://medium.com/@slavahead?source=post_page---byline--43ddf48d2d32--------------------------------)![Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--43ddf48d2d32--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--43ddf48d2d32--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--43ddf48d2d32--------------------------------) [Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--43ddf48d2d32--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--43ddf48d2d32--------------------------------) ·7 分钟阅读·2024 年 3 月 13 日

--

![](img/8c8018c09601a9fce07a351aebeee8eb.png)

# 介绍

我们生活在一个每天大量生成数据的世界。在大型企业中，几乎不可能将所有数据存储在一台服务器上。这就是为什么我们需要**水平扩展**，即将每部分数据存储在不同的服务器上。

与**垂直扩展**（vertical scaling）不同，垂直扩展只需将所有数据存储在单一位置，而在水平扩展（horizontal scaling）中，必须以一种能够在不同服务器上快速访问数据的方式组织存储。通过理解简单系统实现的性能缺陷，我们将设计一个具有弹性的系统，以缓解这些问题。

在系统设计中，我们将使用的原则称为**一致性哈希**（consistent hashing）。

# 问题

假设我们有 n 个数据对象需要存储在 k 台不同的服务器上。服务器的配置可能会随着时间的推移发生变化：

+   任何服务器都可以关闭；

+   可以向系统中添加一台新服务器。
