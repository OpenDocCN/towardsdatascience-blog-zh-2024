# SQL 解释：规范化范式

> 原文：[https://towardsdatascience.com/sql-explained-normal-forms-e2a8b8ce1122?source=collection_archive---------9-----------------------#2024-05-29](https://towardsdatascience.com/sql-explained-normal-forms-e2a8b8ce1122?source=collection_archive---------9-----------------------#2024-05-29)

![](../Images/34e9cd23d967f10a6359526963a89744.png)

图像来源：AI（Dalle-3）

## 将第一、第二和第三范式应用于数据库

[](https://medium.com/@thomas_reid?source=post_page---byline--e2a8b8ce1122--------------------------------)[![Thomas Reid](../Images/c1b4e5f577272633ba07e5dbfd21c02d.png)](https://medium.com/@thomas_reid?source=post_page---byline--e2a8b8ce1122--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e2a8b8ce1122--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e2a8b8ce1122--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--e2a8b8ce1122--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e2a8b8ce1122--------------------------------) ·阅读时间 10 分钟·2024年5月29日

--

数据库系统中的规范化指的是将数据结构化，以减少数据冗余并提高数据完整性的过程。

它涉及将数据库分解为多个表，并根据一组称为范式的规则定义表之间的关系。

目标是消除冗余数据，并确保数据之间的相互依赖关系是合理的。

尽管有许多不同类型的规范化范式，但对于大多数数据库系统来说，应用前三种范式（1NF、2NF 和 3NF）就足以实现一个结构良好且优化的数据库设计。

在本文中，我们将从一个非常简单的单表开始，该表未经过规范化，然后对其应用三种范式，并对从该过程中得到的任何后续表格进行处理，看看最终的设计结果如何。

在此之前，简要解释一下 1NF、2NF 和 3NF。

## 第一范式（1NF）

为了使表格符合 1NF，它必须满足以下标准。

+   每一行必须是唯一可识别的。这***可以***通过在表格的一个或多个列上定义主键来实现。
