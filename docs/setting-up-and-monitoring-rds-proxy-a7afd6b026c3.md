# 设置和监控 RDS Proxy

> 原文：[`towardsdatascience.com/setting-up-and-monitoring-rds-proxy-a7afd6b026c3?source=collection_archive---------6-----------------------#2024-09-17`](https://towardsdatascience.com/setting-up-and-monitoring-rds-proxy-a7afd6b026c3?source=collection_archive---------6-----------------------#2024-09-17)

## 反思使用 AWS 管理的 DB Proxy 时的经验教训

[](https://trrhodes.medium.com/?source=post_page---byline--a7afd6b026c3--------------------------------)![Ross Rhodes](https://trrhodes.medium.com/?source=post_page---byline--a7afd6b026c3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a7afd6b026c3--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a7afd6b026c3--------------------------------) [Ross Rhodes](https://trrhodes.medium.com/?source=post_page---byline--a7afd6b026c3--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a7afd6b026c3--------------------------------) ·7 分钟阅读·2024 年 9 月 17 日

--

补充亚马逊 Web 服务（AWS）[关系数据库服务](https://aws.amazon.com/rds/)（RDS），Proxy 在 2020 年 6 月正式在 RDS for MySQL 和 PostgreSQL 以及它们的 Aurora 兼容版本中推出。Proxy 促进了数据库连接的池化和共享，对于需要大规模查询 RDS 数据库并具有托管故障切换功能的无服务器应用程序非常有价值。自 2020 年 6 月以来，Proxy 已扩展支持 RDS for MariaDB 和 SQL Server。

RDS Proxy 已经由优秀的文档提供支持——[亚马逊自家的产品文档](https://aws.amazon.com/rds/proxy/)是一个有用的参考。本文基于现有资料，重点介绍了设置和监控 Proxy 时的经验教训。这些观察来自于在 Aurora MySQL 上使用 Proxy 的经验，但无论数据库引擎的目标是什么，都适用。

![](img/6a9321e06e8f97bb1f0b5a753c0a3cb3.png)

图片来自 [Growtikavia Unsplash](https://unsplash.com/photos/a-blue-and-white-logo-ahgsuFHlIFo).

在我们深入探讨经验教训之前，有[几个 Proxy 术语](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-proxy.howitworks.html)需要解释。首先，数据库（DB）实例被称为 *目标*，并且目标与 Proxy 的 *目标组* 相关联。每个目标组由一个单独的 RDS DB 实例或一个 RDS DB 集群组成，其中集群包含多个 DB 实例。Proxy 使用一个或多个 *端点* 来将查询转发到目标组。

# 高可用性…
