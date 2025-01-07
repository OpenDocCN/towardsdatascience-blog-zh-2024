# 如何通过 REST API 暴露 Delta 表

> 原文：[https://towardsdatascience.com/how-to-expose-delta-tables-via-rest-apis-53b4dd7afa4e?source=collection_archive---------0-----------------------#2024-05-06](https://towardsdatascience.com/how-to-expose-delta-tables-via-rest-apis-53b4dd7afa4e?source=collection_archive---------0-----------------------#2024-05-06)

## 三种架构讨论并测试用于服务 Delta 表

[](https://rebremer.medium.com/?source=post_page---byline--53b4dd7afa4e--------------------------------)[![René Bremer](../Images/e422c4b84e225d2a949251ebc24dbd2c.png)](https://rebremer.medium.com/?source=post_page---byline--53b4dd7afa4e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--53b4dd7afa4e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--53b4dd7afa4e--------------------------------) [René Bremer](https://rebremer.medium.com/?source=post_page---byline--53b4dd7afa4e--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--53b4dd7afa4e--------------------------------) ·7分钟阅读·2024年5月6日

--

![](../Images/1c9e1d23ea5b1cf0a55e7475f13b53a9.png)

通过内外数据暴露——图片由 [Joshua Sortino on Unsplash](https://unsplash.com/@sortino) 提供

# 1\. 引言

[金奖架构](https://www.databricks.com/glossary/medallion-architecture) 中的 Delta 表通常用于创建数据产品。这些数据产品用于数据科学、数据分析和报告。然而，一个常见的问题是如何通过 REST API 暴露数据产品。这个想法是将这些 API 嵌入具有更严格性能要求的 Web 应用程序中。以下是一些重要问题：

+   从 Delta 表读取数据是否足够快以支持 Web 应用程序？

+   是否需要计算层以使解决方案更具可扩展性？

+   是否需要存储层来满足严格的性能要求？

为了深入探讨这些问题，评估了三种架构，如下所示：架构 A——API 中的库，架构 B——计算层，架构 C——存储层。请参阅下图。

![](../Images/4660f725419bf5d6260407749ecb8d6f.png)

三种架构用于暴露 Delta 表——图片由作者提供

在博客文章的其余部分中，将描述、部署和测试这三种架构。然后得出结论。

# 2\. 架构描述

## 2.1 架构 A：使用 DuckDB 和 PyArrow 的 API 中的库

在此架构中，API 直接连接到 delta 表，中间没有计算层。这意味着数据通过 API 本身的内存和计算进行分析。为了提高性能，使用了[嵌入式数据库 DuckDB](https://duckdb.org/why_duckdb#simple)和[PyArrow](https://pyarrow.readthedocs.io/en/latest/)的 Python 库。这些库确保只加载相关的数据（例如，只加载 API 所需的列）。

![](../Images/60464d2733670c853ce8caa4d24451df.png)

架构 A：API 中的库 — 作者提供的图像

这种架构的优点是数据不需要复制，并且 API 和 delta 表之间不需要中间层。这意味着移动部件更少。

这种架构的缺点是它更难扩展，所有的工作都需要在 API 本身的计算和内存中完成。如果需要分析大量数据，这尤其具有挑战性。这些数据可能来自大量记录、大量列和/或大量并发请求。

## 2.2 架构 B：使用 Synapse、Databricks 或 Fabric 的计算层

在此架构中，API 连接到计算层，而不是直接连接到 delta 表。计算层从 delta 表中获取数据并进行分析。计算层可以是[Azure Synapse](https://learn.microsoft.com/en-us/azure/synapse-analytics/overview-what-is)、[Azure Databricks](https://learn.microsoft.com/en-us/azure/databricks/introduction/)或[Microsoft Fabric](https://learn.microsoft.com/en-us/fabric/get-started/microsoft-fabric-overview)，并且通常能够很好地扩展。数据不会复制到计算层，但可以在计算层应用缓存。在本文的剩余部分中，测试使用了[Synapse 无服务器](https://learn.microsoft.com/en-us/azure/synapse-analytics/sql/on-demand-workspace-overview)。

![](../Images/dfc98b85a7d99735689388832e72f6e6.png)

架构 B：计算层 — 作者提供的图像

这种架构的优点是数据不需要复制，架构能够很好地扩展。此外，它可以用于处理大数据集。

这种架构的缺点是需要在 API 和 delta 表之间添加一个额外的层。这意味着需要维护和保护更多的移动部件。

## 2.3 架构 C：使用 Azure SQL 或 Cosmos DB 的优化存储层

在此架构中，API 不是连接到 delta 表，而是连接到另一个存储层，在该存储层中，delta 表被复制。不同的存储层可以是 Azure SQL 或 Cosmos DB。存储层可以针对快速检索数据进行优化。本文的其余部分中，测试使用了 Azure SQL。

![](../Images/84a687b97eb88e5346449d5e6a4704fb.png)

架构 C：优化存储层 — 作者提供的图像

这种架构的优点是存储层可以通过使用索引、分区和物化视图来优化数据读取速度。这通常是请求-响应型 Web 应用场景中的需求。

这种架构的缺点是需要对数据进行重复存储，并且在 API 和 Delta 表之间需要一个额外的层。这意味着需要维护和保护更多的组件。

在接下来的博客中，将部署和测试这些架构。

# 3. 部署和测试架构

## 3.1 部署架构

为了部署这些架构，创建了一个 GitHub 项目，该项目部署了前一章中讨论的三种解决方案。该项目可以通过以下链接找到：

```py
https://github.com/rebremer/expose-deltatable-via-restapi
```

执行 GitHub 项目时将部署以下内容：

+   一个源自标准测试数据集 [WideWorldImportersDW full](https://azuresynapsestorage.blob.core.windows.net/sampledata/WideWorldImportersDW/csv/full/WideWorldImportersDW/csv/full/fact_sale_1y_full/) 的 Delta 表。该测试数据集包含 5000 万条记录和 22 个列，其中包含一个大描述列。该 Delta 表将使用年份和季度进行分区。

+   所有架构： [Azure Function](https://learn.microsoft.com/en-us/azure/azure-functions/functions-overview?pivots=programming-language-python) 作为 API。

+   架构 B：Synapse Serverless 作为计算层。

+   架构 C：Azure SQL 作为优化存储层。

一旦部署完成，就可以执行测试。测试将在下一个段落中描述。

## 3.2 测试架构

为了测试架构，将应用不同类型的查询和不同的扩展方式。不同类型的查询可以描述如下：

+   查找 20 条记录，每条记录包含 11 个小列（字符型、整数型、日期时间型）。

+   查找 20 条记录，其中 2 列包含一个大描述列，每个字段超过 500 个字符。

+   使用 group by、having、max、average 等进行数据聚合。

以下是查询的示例。

```py
-- Query 1: Point look up 11 columns without large texts
SELECT SaleKey, TaxAmount, CityKey, CustomerKey, BillToCustomerKey, SalespersonKey, DeliveryDateKey, Package 
FROM silver_fact_sale
WHERE CityKey=41749 and SalespersonKey=40 and CustomerKey=397 and TaxAmount > 20
-- Query 2: Description column with more than 500 characters
SELECT SaleKey, Description 
FROM silver_fact_sale
WHERE CityKey=41749 and SalespersonKey=40 and CustomerKey=397 and TaxAmount > 20
-- Query 3: Aggregation
SELECT MAX(DeliveryDateKey), CityKey, AVG(TaxAmount)
FROM silver_fact_sale
GROUP BY CityKey
HAVING COUNT(CityKey) > 10
```

扩展性可以描述如下：

+   对于架构 A，数据处理将在 API 本身中完成。这意味着将使用 API 的 [应用服务计划](https://learn.microsoft.com/en-us/azure/app-service/overview-hosting-plans) 来利用 API 的计算和内存资源。将使用 [SKU Basic](https://azure.microsoft.com/en-us/pricing/details/app-service/windows/#pricing)（1 核心和 1.75 GB 内存）和 SKU P1V3 SKU（2 核心，8 GB 内存）进行测试。对于架构 B 和 C，这不相关，因为处理是在其他地方进行的。

+   对于架构 B，使用 Synapse Serverless。扩展将自动完成。

+   对于架构 C，使用 [标准层](https://learn.microsoft.com/en-us/azure/azure-sql/database/service-tiers-dtu?view=azuresql) 的 Azure SQL 数据库，提供 125 DTU。将分别测试没有索引和在 CityKey 上有索引的情况。这个索引可以在之后应用，并且不需要重新分区数据。

下一段将描述结果。

## 3.3 结果

部署并测试架构后，可以获得结果。这是结果的总结：

![](../Images/0881c24c646ce4ade82355446a0f7a03.png)

测试结果总结

架构 A 不能使用 SKU B1 部署。如果使用 SKU P1V3，则在列大小不太大的情况下，结果可以在 15 秒内计算出来。请注意，所有数据都在 API 应用服务计划中进行分析。如果加载了过多数据（无论是通过很多行、大列和/或大量并发请求），这个架构将难以扩展。

使用 Synapse Serverless 的架构 B 在 10 到 15 秒内完成计算。计算在自动扩展的 Synapse Serverless 上进行，用于提取和分析数据。所有三种类型的查询的性能是一致的。

使用 Azure SQL 的架构 C 在创建索引时表现最佳。对于查询 1 和查询 2，API 的响应时间大约为 1 秒。查询 3 需要进行全表扫描，其性能与其他解决方案相当。

# 3. 结论

[Medallion 架构](https://www.databricks.com/glossary/medallion-architecture)中的 Delta 表通常用于创建数据产品。这些数据产品用于数据科学、数据分析和报告。然而，一个常见的问题是如何通过 REST API 暴露 delta 表。在这篇博客中，描述了三种架构及其优缺点。

**架构 A：使用 DuckDB 和 PyArrow 的 API 中的库**。在这个架构中，API 直接连接到 delta 表，并且中间没有任何层级。这意味着所有的数据都在 Azure Function 的内存和计算中进行分析。

+   这个架构的优点是无需额外的资源。这意味着需要维护和保护的部件更少。

+   这个架构的缺点是它的扩展性差，因为所有的数据都必须在 API 本身进行分析。因此，它只能用于少量的数据。

**架构 B：使用 Synapse、Databricks 或 Fabric 的计算层**。在这个架构中，API 连接到计算层。这个计算层从 delta 表中提取和分析数据。

+   这个架构的优点是它具有良好的扩展性，且数据不会重复。它非常适合需要聚合并处理大数据集的查询。

+   这个架构的缺点是，对于查询操作，无法始终在 5 秒内返回响应。此外，还需要确保和维护额外的资源。

**架构 C：使用 Azure SQL 或 Cosmos DB 的优化存储层**。

在这个架构中，API 连接到一个优化过的存储层。Delta 表预先复制到这个存储层，并且该存储层用于提取和分析数据。

+   这种架构的优点在于，可以通过使用索引、分区和物化视图来优化查询性能。这通常是请求-响应型 Web 应用的要求。

+   这种架构的缺点在于，数据需要复制到另一个存储层，并且需要保持同步。此外，还需要保障和维护额外的资源。

不幸的是，并没有一种“灵丹妙药”式的解决方案。本文旨在为通过 REST API 暴露增量表选择最佳架构提供指导。
