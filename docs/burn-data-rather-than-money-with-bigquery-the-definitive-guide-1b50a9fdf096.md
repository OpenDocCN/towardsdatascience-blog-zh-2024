# 一份关于如何高效使用BigQuery的终极指南

> 原文：[https://towardsdatascience.com/burn-data-rather-than-money-with-bigquery-the-definitive-guide-1b50a9fdf096?source=collection_archive---------2-----------------------#2024-03-03](https://towardsdatascience.com/burn-data-rather-than-money-with-bigquery-the-definitive-guide-1b50a9fdf096?source=collection_archive---------2-----------------------#2024-03-03)

## 充分利用你的BigQuery使用，烧掉数据而不是烧掉钱，用一些实用技巧创造真正的价值。

[](https://vojay.medium.com/?source=post_page---byline--1b50a9fdf096--------------------------------)[![Volker Janz](../Images/0825160d6d521f4152948f0187cf354b.png)](https://vojay.medium.com/?source=post_page---byline--1b50a9fdf096--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1b50a9fdf096--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1b50a9fdf096--------------------------------) [Volker Janz](https://vojay.medium.com/?source=post_page---byline--1b50a9fdf096--------------------------------)

· 发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1b50a9fdf096--------------------------------) · 20分钟阅读 · 2024年3月3日

--

· [📝 引言](#b5f1)

· [💎 BigQuery基础和理解成本](#ecac)

∘ [存储](#2451)

∘ [计算](#73e8)

· [📐 数据建模](#c6f3)

∘ [数据类型](#5d43)

∘ [向去规范化转变](#20ab)

∘ [分区](#acf8)

∘ [聚类](#8d1d)

∘ [嵌套重复列](#2c8a)

∘ [索引](#7ed9)

∘ [物理字节存储计费](#507a)

∘ [使用主键和外键的连接优化](#258d)

· [⚙️ 数据操作](#6bae)

∘ [复制数据/表](#28ad)

∘ [加载数据](#da01)

∘ [删除分区](#0ef6)

∘ [获取表的不同分区](#0c4d)

∘ [不要持久化计算的度量](#3ae7)

· [📚 摘要](#59d4)

∘ [遵循数据建模最佳实践](#165f)

∘ [掌握数据操作以实现成本效益](#8018)

∘ [为了效率而设计，避免不必要的数据持久化](#af6f)

**免责声明**：BigQuery是一个不断发展的产品，定价可能随时变化，本文基于我个人的经验。

![](../Images/ad3d101dc043e43fc1b8f1c82871e0d7.png)

由[Konstantin Evdokimov](https://unsplash.com/@constantinevdokimov?utm_source=medium&utm_medium=referral)拍摄，照片来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 📝 引言

在数据仓库领域，有一个普遍的真理：管理数据可能是昂贵的。就像一条守护财宝的龙，每个存储的字节和每个执行的查询都需要它的金币份额。但是让我给你一个魔法咒语来安抚这条龙：烧掉数据，而不是烧掉钱！

在本文中，我们将揭开 BigQuery 魔法的艺术，旨在在提高效率的同时降低成本，甚至更多。加入我们，一同探索成本优化的深度，在这里，每个字节都是珍贵的硬币。

![](../Images/26edd2af9f5eda4849e2b289d311ab64.png)

图片由 [Jonathan Kemper](https://unsplash.com/@jupp?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 💎 BigQuery 基础和成本理解

BigQuery 不仅仅是一个工具，而是一整套可扩展的计算和存储技术包，配备快速网络，一切由谷歌管理。在其核心，BigQuery 是一个无服务器的数据仓库，专为分析目的而设计，内置特性如机器学习（*BigQuery ML*）。BigQuery 将存储和计算分开，通过谷歌的 Jupiter 网络进行连接，以利用 1 Petabit/秒的总双向带宽。存储系统使用 Capacitor，这是谷歌为半结构化数据提供的专有列式存储格式，底层的文件系统是谷歌的分布式文件系统 Colossus。计算引擎基于 Dremel，并使用 Borg 进行集群管理，能够跨多个集群运行成千上万的 Dremel 作业。

> ***BigQuery 不仅仅是一个工具，而是一整套可扩展的计算和存储技术包，配备快速网络，一切由谷歌管理***

以下插图展示了 BigQuery 架构的基本结构：

![](../Images/348b1c767b85af6fbb00e012327b8993.png)

BigQuery 架构（作者）

数据可以存储在 Colossus 中，但也可以在 Google Cloud Storage 中创建 BigQuery 表。在这种情况下，查询仍然通过 BigQuery 计算基础设施处理，但读取的数据来自 GCS。此类 `外部表` 有一些缺点，但在某些情况下，将数据存储在 GCS 中可能更具成本效益。另外，有时候并不是关于大数据，而仅仅是从现有的 CSV 文件中读取数据，这些文件以某种方式被导入到 GCS。为了简便起见，使用这种表格也可能带来好处。

![](../Images/f263f69736f23df70761a9222df7e3b2.png)

BigQuery 外部表（作者）

要充分发挥 BigQuery 的潜力，常见的做法是将数据存储在 BigQuery 存储中。

成本的主要驱动因素是存储和计算，谷歌不会对其他部分收费，比如存储与计算之间的网络传输。

## 存储

存储费用为每 GB $0.02 — 每 GB $0.04 为活跃数据，和每 GB $0.01 — 每 GB $0.02 为非活跃数据（*即在过去 90 天内未被修改的数据*）。如果你的表或分区在连续 90 天内没有修改，则被视为长期存储，存储价格将自动下降 50%。折扣是基于每个表或每个分区应用的。修改操作会重置 90 天计时器。

## 计算

BigQuery 按扫描的数据量收费，而不是查询的运行时，也不会对从存储到计算集群的传输收费。计算成本取决于位置，例如 `europe-west3` 的费用为每 TB $8.13。

这意味着：

> ***我们希望最小化每个查询要扫描的数据量！***

![](../Images/fb555e76a253c89bb09904ccc83e1e24.png)

左： [Jp Valery](https://unsplash.com/de/@jpvalery?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在 [Unsplash](https://unsplash.com/de/fotos/zeitrafferfotografie-mehrerer-brennender-us-dollar-banknoten-blOLCO2K4M0?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)，右： [Gabriel Jimenez](https://unsplash.com/de/@gabrielj_photography?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在 [Unsplash](https://unsplash.com/de/fotos/bokeh-fotografie-einer-person-die-erde-tragt-jin4W1HqgL4?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

在执行查询时，BigQuery 会估算要处理的数据量。在 BigQuery Studio 查询编辑器中输入查询后，你可以在右上角看到估算值。

![](../Images/51bf962d037887c304ce20cda8662211.png)

BigQuery Studio

如果像上图所示显示为 1.27 GB，并且查询在 `europe-west3` 位置处理，则可以按以下方式计算费用：

```py
1.27 GB / 1024 = 0.0010 TB * $8.13 = $0.0084 total costs
```

估算值通常是一个悲观的计算，优化器通常能够利用缓存结果、物化视图或其他技术，从而使实际计费字节数低于估算值。检查这个估算值仍然是一个好的做法，可以让你大致了解工作影响。

你也可以为查询设置最大计费字节数。如果查询超过该限制，它将失败，并且不会产生任何费用。这个设置可以通过导航到 更多 -> 查询设置 -> 高级选项 -> 最大计费字节数 来更改。

![](../Images/31c6507a2b70acf6778a6ef827a030bd.png)

BigQuery 查询设置

![](../Images/cbe8c884ffa43f6cf07d42f442d90060.png)

BigQuery 超过了计费字节数的限制

不幸的是，直到现在，无法为每个查询设置默认值。只能限制每个用户每天每个项目的计费字节数，或限制一个项目每天的所有字节总计。

当你第一次开始使用 BigQuery 进行项目时，你很可能会选择按需计算定价模型。在按需定价模型下，你通常可以访问最多 2000 个并发槽，这些槽会在单个项目的所有查询之间共享，这在大多数情况下是足够的。一个槽类似于一个虚拟 CPU，处理查询 DAG 的一单位工作。

当每月支出达到一定金额时，值得考虑容量定价模型，这样可以让成本更具可预测性。

# 📐 数据建模

## 数据类型

为了减少存储和计算成本，始终使用最小的数据类型对于你的列非常重要。你可以轻松地通过以下概览估算一定数量行数据的成本：

```py
Type       | Size
-----------|---------------------------------------------------------------
ARRAY      | Sum of the size of its elements 
BIGNUMERIC | 32 logical bytes
BOOL       | 1 logical byte
BYTES      | 2 logical bytes + logical bytes in the value
DATE       | 8 logical bytes
DATETIME   | 8 logical bytes
FLOAT64    | 8 logical bytes
GEOGRAPHY  | 16 logical bytes + 24 logical bytes * vertices in the geo type
INT64      | 8 logical bytes
INTERVAL   | 16 logical bytes
JSON       | Logical bytes in UTF-8 encoding of the JSON string
NUMERIC    | 16 logical bytes
STRING     | 2 logical bytes + the UTF-8 encoded string size
STRUCT     | 0 logical bytes + the size of the contained fields
TIME       | 8 logical bytes
TIMESTAMP  | 8 logical bytes
```

`*NULL*` *被计算为0逻辑字节*

**示例**：

```py
CREATE TABLE gold.some_table (
  user_id INT64,
  other_id INT64,
  some_String STRING, -- max 10 chars
  country_code STRING(2),
  user_name STRING,   -- max 20 chars
  day DATE
);
```

通过这个定义和数据类型表，能够估算100,000,000行数据的逻辑大小：

```py
100.000.000 rows * (
  8 bytes (INT64) +
  8 bytes (INT64) +
  2 bytes + 10 bytes (STRING) +
  2 bytes + 2 bytes (STRING(2)) +
  2 bytes + 20 bytes (STRING) +
  8 bytes (DATE)
) = 6200000000 bytes / 1024 / 1024 / 1024
  = 5.78 GB
```

假设我们在这个表上执行`SELECT *`，它将花费我们5.78 GB / 1024 = 0.0056 TB * $8.13 = $0.05，在`europe-west3`区域。

在设计数据模型之前进行这些计算是一个好主意，这不仅有助于优化数据类型的使用，还能估算你所从事项目的成本。

## 向反规范化转变

在数据库设计和管理领域，数据规范化和反规范化是旨在优化数据结构以实现高效存储、检索和操作的基本概念。传统上，规范化被誉为最佳实践，强调减少冗余并保持数据完整性。然而，在BigQuery和其他现代数据仓库的背景下，动态发生变化，反规范化往往成为首选的方法。

在规范化数据库中，数据被组织成多个表，每个表代表一个独立的实体或概念，并通过一对一、一对多或多对多等关系进行连接。这种方法遵循数据库规范化形式的原则，如第一范式（1NF）、第二范式（2NF）和第三范式（3NF）等。

这带来了减少冗余、数据完整性以及因此减少存储使用的优势。

![](../Images/da2e4c1933ff53547b874460d16bfabc.png)

图片由[Shubham Dhage](https://unsplash.com/@theshubhamdhage?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

尽管数据规范化在传统关系型数据库中具有优势，但在处理像BigQuery这样的现代分析平台时，范式发生了转变。BigQuery旨在处理海量数据并执行大规模的复杂分析查询。在这种环境下，重点从最小化存储空间转向优化查询性能。

在BigQuery中，反规范化成为首选策略有几个原因：

+   **查询性能**：BigQuery的分布式架构在并行扫描大量数据方面表现出色。反规范化表减少了复杂连接的需求，从而缩短查询执行时间。

+   **成本效益**：通过减少查询处理所需的计算资源，反规范化可以带来成本节省，因为BigQuery中的查询成本是基于处理的数据量计算的。

+   **简化数据建模**：非规范化表简化了数据建模过程，使设计和维护分析用途的架构变得更加容易。

+   **优化分析工作负载**：非规范化结构非常适合分析工作负载，在这种负载中，聚合、转换和复杂查询是常见的。

此外，存储比计算便宜得多，这意味着：

> ***对于预先连接的数据集，你可以用存储资源换取计算资源！***

![](../Images/37e6cbd109fc824810306d381286db7c.png)

非规范化（作者观点）

虽然非规范化并不是一种适合所有情况的解决方案，但它应该被考虑用于成本和性能优化。然而，也有一些方面可能导致不同的、成本效益更高的设计。

特别是当在`JOIN`的**右侧**有较小的表时，BigQuery 利用**广播连接**将表的完整数据集广播到每个处理较大表的槽。通过这种方式，规范化不会对性能产生负面影响。事实上，情况恰恰相反，因为数据冗余减少了。

当 BigQuery 不使用广播连接时，它会采用**哈希连接**方法。在这种情况下，BigQuery 使用哈希和洗牌操作，以便匹配的键在同一槽中处理，从而执行本地连接。然而，与广播连接相比，这可能是一个代价高昂的操作，因为需要移动数据。

如果你发现自己处于哈希连接被使用的情况，仍然有方法可能改善性能。至少可以将连接列定义为集群列。这样可以将数据放置在同一列存储文件中，减少洗牌的影响。

最终，最佳方法取决于数据模型的具体情况以及规范化表的大小。如果通过规范化结构可以减少冗余，同时保持`JOIN`表的大小较小，从而使用广播连接，那么这比强制执行非规范化方法更为优越。然而，对于大于10GB的表，应该通过具体的基准测试来评估，这也引出了黄金法则：

**基准测试是关键！** 不要仅仅依赖理论。测试不同的方法（规范化、非规范化、嵌套/重复），以找到最适合你具体用例的高效解决方案。

## 分区

分区将表划分为基于**一个**特定列的多个段。分区列可以使用以下三种方法之一：

🗂️ **整数范围分区**：根据整数列的范围进行分区，范围包括起始值、结束值和间隔

⏰ **时间单位分区**：按日期、时间戳或日期时间列对表进行分区，粒度可以是每小时、每日、每月或每年

⏱️ **摄取时间分区**：根据当前时间，使用名为`_PARTITIONTIME`的伪列在插入数据时自动分配分区

由你来定义分区列，但强烈建议明智地选择该列，因为这可以减少处理/计费的字节数。

![](../Images/ab874087ba6efbc653ba93c0707b36c0.png)

分区示例（按作者）

**示例：**

```py
CREATE TABLE IF NOT EXISTS silver.some_partitioned_table (
  title STRING,
  topic STRING,
  day DATE
)
PARTITION BY day
OPTIONS (
  partition_expiration_days = 365
);
```

在上面的示例中，你还可以看到如何设置 `partition_expiration_days` 选项，该选项会删除超过 X 天的分区。

## 聚类

聚类根据一个或多个列在每个分区内对数据进行排序。当在查询筛选中使用聚类列时，这项技术将加速执行，因为 BigQuery 可以确定扫描哪些数据块。特别推荐在高基数列中使用该技术，例如以下示例中的 `title` 列。

你可以定义最多 **四个** 聚类列。

**示例：**

```py
CREATE TABLE IF NOT EXISTS silver.some_partitioned_table (
  title STRING,
  topic STRING,
  day DATE
)
PARTITION BY day
CLUSTER BY topic
OPTIONS (
  partition_expiration_days = 365
);
```

## 嵌套重复列

数据反规范化通常也会引入信息的重复。数据冗余会增加额外的存储空间和查询时需要处理的字节数。然而，有一种方法可以在没有冗余的情况下使用嵌套重复列实现反规范化的表设计。

**嵌套**列使用 `struct` 类型并将某些属性组合成一个对象。嵌套的 **重复** 列是一个 `struct` 数组，为表格中的单行存储。例如：如果你有一个表格，每行存储一个用户的登录记录，包括用户 ID 和该用户的注册国家，那么你就会在每个用户的每次登录中出现 ID 和国家的冗余。

与其为每次登录存储一行数据，使用嵌套重复列，你可以为每个用户存储一行数据，并在一个类型为 `ARRAY<STRUCT<...>>` 的列中存储该用户的所有登录记录。该结构体包含与登录相关的所有属性，例如日期和设备。以下插图可视化了这个示例：

![](../Images/f6562058c8182f92d6a5db94c78706eb.png)

嵌套重复列示例（按作者）

**示例：**

```py
CREATE TABLE silver.logins (
    user_id INT64,
    country STRING(2),
    logins ARRAY<STRUCT<
        login_date DATE,
        login_device STRING
    >>,
    day DATE
)
PARTITION BY day
CLUSTER BY country, user_id
OPTIONS (
    require_partition_filter=true
);
```

上面的示例还展示了 `require_partition_filter` 的使用，该功能会阻止任何不对分区列进行筛选的查询。

这种数据建模技术可以显著减少存储和处理的字节数。然而，它并不是所有反规范化或数据建模场景的万能解决方案。主要的缺点是：**你不能在结构体属性上设置聚类或分区列**。

这意味着：在上面的示例中，如果用户按 `login_device` 进行筛选，则需要进行全表扫描，而且我们没有通过聚类进行优化的选项。特别是当你的表格被用作 Excel 或 PowerBI 等第三方软件的数据源时，这可能会成为一个问题。在这种情况下，你应仔细评估通过嵌套重复列去除冗余的好处是否足以弥补无法通过聚类进行优化的缺点。

## 索引

通过在一个或多个列上定义搜索索引，BigQuery 可以利用此索引加速使用 `SEARCH` 函数的查询。

可以使用`CREATE SEARCH INDEX`语句创建搜索索引：

```py
CREATE SEARCH INDEX example_index ON silver.some_table(ALL COLUMNS);
```

使用`ALL COLUMNS`时，索引会自动为所有`STRING`和`JSON`列创建。你也可以更有选择性地只为特定列添加列名列表。使用`SEARCH`功能，索引可以在所有或特定列中进行搜索：

```py
SELECT * FROM silver.some_table WHERE SEARCH(some_table, 'needle');
```

一项新功能，在撰写本文时处于预览状态，允许将索引用于如`=`、`IN`、`LIKE`和`STARTS_WITH`等操作符。这对于那些通过像PowerBI或Excel这样的第三方工具直接供最终用户使用的数据结构非常有利，可以进一步提高速度并降低某些过滤操作的成本。

关于这一点的更多信息可以在[官方搜索索引文档](https://cloud.google.com/bigquery/docs/search-index)中找到。

## 物理字节存储计费

BigQuery提供了两种存储计费模型：标准模型和物理字节存储计费模型。选择合适的模型取决于你的数据访问模式和压缩能力。

标准模型非常直接。你按每GB数据支付固定费用，如果数据在90天内未修改，还会有轻微的折扣。这种方式简单易用，不需要管理不同的存储类别。然而，如果你的数据高度压缩，或者你不经常访问它，这种模型可能会更昂贵。

物理字节存储计费采用不同的方法。你支付的费用是基于数据在磁盘上占据的物理空间，而不是基于存储的逻辑数据量，无论你访问它的频率如何，或者它压缩得有多好。对于高度压缩的数据或不经常访问的数据，这种模型**可能会便宜得多**。然而，它要求你管理两种独立的存储类别：一种是频繁访问的数据，另一种是长期存储的数据，这可能会增加复杂性。

那么，你应该选择哪个模型呢？**以下是一个简明指南**：

**如果选择标准模型**：

+   你的数据没有高度压缩。

+   你更倾向于选择简单且易于管理的方法。

**如果选择PBSB模型**：

+   你的数据高度压缩。

+   你能够管理不同的存储类别来优化成本。

你可以在数据集的高级选项中更改计费模型。你还可以在表格详细信息视图中检查逻辑字节与物理字节的差异，这使得选择模型更为方便。

![](../Images/4cb2180cfd099101aac3829db377d55c.png)

存储计费模型的数据集高级选项

## 主键和外键的连接优化

自[2023年7月起，BigQuery引入了非强制性的主键和外键约束](https://cloud.google.com/blog/products/data-analytics/join-optimizations-with-bigquery-primary-and-foreign-keys?hl=en)。请记住，BigQuery并不是一个经典的关系数据库管理系统，尽管使用此功能定义数据模型可能会让你觉得它是。

如果这些键没有强制执行，并且这不是我们熟悉的关系数据库，那么意义何在？答案是：查询优化器可以利用这些信息来更好地优化查询，特别是在内部连接消除、外部连接消除和连接重排序等概念上。

定义约束类似于其他 SQL 方言，只不过你必须将其指定为 `NOT ENFORCED`：

```py
CREATE TABLE gold.inventory (
 date INT64 REFERENCES dim_date(id) NOT ENFORCED,
 item INT64 REFERENCES item(id) NOT ENFORCED,
 warehouse INT64 REFERENCES warehouse(id) NOT ENFORCED,
 quantity INT64,
 PRIMARY KEY(date, item, warehouse) NOT ENFORCED
);
```

# ⚙️ 数据操作

## 复制数据 / 表

从一个地方复制数据到另一个地方是我们作为数据工程师日常工作的一部分。假设任务是将名为 `bronze` 的 BigQuery 数据集中的数据复制到另一个名为 `silver` 的数据集，且该数据集位于名为 `project_x` 的 Google Cloud Platform 项目中。简单的方法是执行如下 SQL 查询：

```py
CREATE OR REPLACE TABLE project_x.silver.login_count AS
SELECT
    user_id,
    platform,
    login_count,
    day
FROM project_x.bronze.login_count;
```

尽管这样可以进行转换，但在许多情况下，我们只是希望将数据从一个地方复制到另一个地方。上面查询的计费字节数基本上是我们需要从源头读取的数据量。然而，我们也可以通过以下查询**免费**获得这些数据：

```py
CREATE TABLE project_x.silver.login_count
COPY project_x.bronze.login_count;
```

或者，可以使用 `bq` CLI 工具来实现相同的结果：

```py
bq cp project_x:bronze.login_count project_x:silver.login_count
```

通过这种方式，你可以以**0费用**复制数据。

## 加载数据

对于数据摄取，Google Cloud Storage 是解决该任务的务实方式。无论是 CSV 文件、来自 Hadoop 生态系统的 ORC / Parquet 文件，还是其他任何来源，都可以轻松上传并以低成本存储数据。

也可以在 GCS 上的数据基础上创建 BigQuery 表。这些**外部**表仍然利用 BigQuery 的计算基础设施，但不提供某些功能和性能。

假设我们从使用 ORC 存储格式的分区 Hive 表上传数据。上传数据可以使用 `distcp` 完成，或者通过先从 HDFS 获取数据，然后使用与 Cloud Storage 交互的可用 CLI 工具将其上传到 GCS。

假设我们有一个包含名为 month 的分区结构，那么文件可能看起来如下：

```py
/some_orc_table/month=2024-01/000000_0.orc
/some_orc_table/month=2024-01/000000_1.orc
/some_orc_table/month=2024-02/000000_0.orc
```

当我们将这些数据上传到 GCS 时，可以像这样创建外部表定义：

```py
CREATE EXTERNAL TABLE IF NOT EXISTS project_x.bronze.some_orc_table
WITH PARTITION COLUMNS
OPTIONS(
  format="ORC",
  hive_partition_uri_prefix="gs://project_x/ingest/some_orc_table",
  uris=["gs://project_x/ingest/some_orc_table/*"]
);
```

它将从 ORC 文件中推导出模式，甚至检测分区列。将这些数据从 GCS 移动到 BigQuery 存储的简单方法现在可能是，在 BigQuery 中创建一个表，然后按照务实的 `INSERT INTO ... SELECT FROM` 方法操作。

然而，类似于前面的例子，计费的字节数将反映存储在 `gs://project_x/ingest/some_orc_table` 中的数据量。还有另一种方式，可以通过使用 `LOAD DATA` SQL 语句以**0费用**实现相同的结果。

```py
LOAD DATA OVERWRITE project_x.silver.some_orc_table (
  user_id INT64,
  column_1 STRING,
  column_2 STRING,
  some_value INT64
)
CLUSTER BY column_1, column_2
FROM FILES (
  format="ORC",
  hive_partition_uri_prefix="gs://project_x/ingest/some_orc_table",
  uris=["gs://project_x/ingest/some_orc_table/*"]
)
WITH PARTITION COLUMNS (
  month STRING
);
```

使用此语句，我们直接获取包含数据的 BigQuery 表，**无需先创建外部表**！此外，此查询**没有费用**。`OVERWRITE` 是可选的，因为数据也可以追加，而不是每次运行时都覆盖表。

如你所见，分区列也可以被指定。虽然不能应用任何转换，但有一个主要优点：我们可以提前定义集群列。这样，我们可以创建一个高效的目标表版本，用于后续的下游处理，且**免费**！

## 删除分区

在某些ETL或ELT场景中，典型的工作流是将表按天分区，然后根据来自临时/摄取表的新数据替换特定分区。

![](../Images/c0c962a5f17c681e88a437f318b19468.png)

分区摄取示例（作者）

BigQuery提供了`MERGE`语句，但简单的方法是先从目标表中删除受影响的分区，然后插入数据。

在这种情况下，删除分区可以这样实现：

```py
DELETE FROM silver.target WHERE day IN (
  SELECT DISTINCT day
  FROM bronze.ingest
);
```

即使`day`是两种情况下的分区列，该操作仍然涉及若干成本。然而，再一次，这里有一个**零成本**的替代方案：

```py
DROP TABLE silver.target$20240101
```

使用`DROP TABLE`，你实际上也可以通过附加后缀`$<partition_id>`来删除单个分区。

当然，上面的示例只是删除一个分区。然而，使用BigQuery的过程语言，我们可以轻松地在循环中执行该语句。

```py
FOR x IN (SELECT DISTINCT day FROM bronze.ingest)
DO
  SELECT x; -- replace with DROP TABLE
END FOR;
```

或者，可以使用Airflow和/或dbt，先选择分区，然后在循环中运行某个模板化查询。

然而，获取分区表的不同分区可以像上述示例那样进行，但即使我们只读取单一列，这仍然会产生一些成本。但再次强调，有一种几乎**免费**的方法来实现这一点，我们将在下一章中探讨。

## 获取表的不同分区

在上面的示例中，我们使用了以下方法来获取分区的不同分区：

```py
SELECT DISTINCT day
FROM bronze.ingest
```

这是我在一个示例用例中查询的成本：

```py
Bytes billed: 149.14 GB (= $1.18 depending on location)
```

BigQuery维护了大量关于表、列和分区的有价值元数据。这些信息可以通过`INFORMATION_SCHEMA`访问。我们可以通过简单地使用这些元数据来实现完全相同的结果：

```py
SELECT PARSE_DATE('%Y%m%d', partition_id) AS day
FROM bronze.INFORMATION_SCHEMA.PARTITIONS
WHERE table_name = 'ingest'
```

与我之前提到的相同用例进行比较，这就是查询的成本：

```py
Bytes billed: 10 MB (= $0.00008 depending on location)
```

如你所见，149GB与10MB之间的差距非常大。通过这种方法，即使是巨大的表，你也可以以**几乎零成本**获取不同的分区。

## 不要持久化计算的度量

当你开始使用BigQuery进行第一个项目时，你很可能会选择按需计算定价模式。使用按需定价，你通常可以访问最多2000个并发槽位，所有查询共享这些槽位。但即使使用容量定价，你也会至少拥有100个槽位。

对于大部分日常的 ETL/ELT 工作负载，这些插槽实际上并不是性能的瓶颈。你可以通过进入 BigQuery -> 管理 -> 监控，选择正确的位置，并在“图表配置”下将图表改为 *插槽使用情况* 来自行检查。在很多情况下，你会惊讶于实际使用的插槽是多么少。

![](../Images/3b574cfdb6e528945373257885f71b59.png)

BigQuery 插槽监控

这与节省成本有什么关系呢？假设你有一个经典的事实表，或者一般的某个表，它提供了某些关键绩效指标（KPI）。这个表随后被用于 Looker、Excel、PowerBI 或其他工具中的分析/报告。

通常，这些工具会自动生成查询来为报告或仪表盘提供所需的数据。这些自动生成的查询可能在应用 BigQuery 最佳实践时并不理想。换句话说，它们可能会扫描比必要更多的数据，从而增加计费的字节数。

我们可以通过在事实表上方引入视图层来避免这一点。通过视图而非实际表提供数据给服务工具是一种非常有价值的最佳实践，因为这为你在模式变更时提供了更多灵活性，同时也能在视图中引入计算度量而不需要持久化数据。

当然，当使用这些度量时，这可能会增加 CPU 使用率，但另一方面，它也能显著减少底层表的总大小。

为了说明这一原则，以以下事实表为基础：

```py
CREATE TABLE IF NOT EXISTS gold.some_fact_table (
  user_id INT64,
  payment_count INT64,
  value_1 INT64,
  value_2 INT64,
  day DATE
)
PARTITION BY day
CLUSTER BY user_id
OPTIONS (
  partition_expiration_days = 365
);
```

基本思路是为访问这些数据的利益相关者引入视图，并通过计算度量对其进行扩展：

```py
CREATE OR REPLACE VIEW gold.some_fact_view AS
SELECT
  user_id,
  payment_count,
  value_1,
  value_2,
  payment_count > 0 AS is_paying_user,
  value_1 + value_2 AS total_value,
  day
FROM gold.some_fact_table;
```

在这个例子中，我们能够避免持久化两个 `INT64` 值。每一个值都使用 8 个逻辑字节。如果我们的事实表有 1,000,000,000 行，那么这意味着我们节省了：

```py
1000000000 rows * 8 B * 2 columns / 1024 / 1024 / 1024 = 15 GB
```

这并不是大量数据，但它可能意味着在某些情况下 BigQuery 必须扫描的 15 GB 数据减少了。实际上，可能存在计算度量，它们能够节省更多的数据扫描量。

# 📚 总结

不要像守护宝藏的巨龙一样囤积每一个字节。相反，学会通过智能管理和优化来“燃烧”数据 🔥。通过采用这种激烈的方法，你将把 BigQuery 从一个成本中心转变为强大的数据探索引擎，让你燃烧的是数据，而不是金钱！

## 采用数据建模最佳实践

+   利用尽可能小的数据类型以最小化存储和处理成本。

+   在适当的时候利用去规范化，以优化查询性能并减少存储使用。

+   实施分区和聚类，使 BigQuery 能够高效地仅扫描查询所需的相关数据。

+   探索嵌套重复列作为消除冗余并保持数据完整性的一种方式，但要注意聚类的限制。

## 掌握数据操作以提高成本效益

+   使用 `CREATE TABLE ... COPY` 或 `bq cp` 命令在表之间复制数据，无需产生费用。

+   使用 `LOAD DATA` 语句直接从 Cloud Storage 加载数据到 BigQuery 表中，且无需付费。

+   利用 `DROP TABLE` 和分区后缀的功能高效地删除特定分区。

+   利用 `INFORMATION_SCHEMA` 获取表的元数据，例如不同的分区值，相比传统查询大大降低成本。

## 设计时要注重效率，避免不必要的数据持久化。

+   实现视图层来提供计算度量的数据，避免存储冗余数据。

+   监控你的 BigQuery 插槽使用情况，以了解插槽限制是否是一个问题，从而让你可以集中精力优化查询结构。

> ***通过采用这些策略，你可以释放 BigQuery 的真正潜力，将其转变为一个高效且具成本效益的数据探索和分析引擎。记住，在 BigQuery 的世界里，关键是消耗数据，而不是金钱！***

**欢迎在评论区分享你的经验！**
