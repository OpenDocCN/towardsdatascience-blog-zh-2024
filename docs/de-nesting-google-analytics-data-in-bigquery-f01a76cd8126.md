# 去嵌套 Google Analytics 数据在 BigQuery 中

> 原文：[`towardsdatascience.com/de-nesting-google-analytics-data-in-bigquery-f01a76cd8126?source=collection_archive---------9-----------------------#2024-03-26`](https://towardsdatascience.com/de-nesting-google-analytics-data-in-bigquery-f01a76cd8126?source=collection_archive---------9-----------------------#2024-03-26)

## 扁平化表格的正确方法

[](https://medium.com/@martin.weitzmann?source=post_page---byline--f01a76cd8126--------------------------------)![Martin Weitzmann](https://medium.com/@martin.weitzmann?source=post_page---byline--f01a76cd8126--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f01a76cd8126--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f01a76cd8126--------------------------------) [Martin Weitzmann](https://medium.com/@martin.weitzmann?source=post_page---byline--f01a76cd8126--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f01a76cd8126--------------------------------) ·阅读时间：5 分钟·2024 年 3 月 26 日

--

![](img/158e6047cc9d047d88417790e20962d6.png)

新加坡的照片由 [Mike Enerio](https://unsplash.com/@mikeenerio?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

BigQuery 是一个优化的分析引擎，适合处理预先连接（或嵌套）数据。在分析场景中，子关系是有意义的，因为我们不想在更大的数据集上进行连接——想象一下过去三年的每日同比比较，聚合着数 TB 的数据——但是连接会增加复杂性。

子关系或子表通常实现为结构体数组。数组作为一种类似列表的数据类型提供行，而结构体类似于映射或字典，提供列。子模式在整个表中是一致的——与 JSON 类型不同，后者可以在每行中改变其模式。

[](/https-medium-com-martin-weitzmann-bigquery-sql-on-nested-data-cf9589c105f4?source=post_page-----f01a76cd8126--------------------------------) ## BigQuery：嵌套数据上的 SQL

### BigQuery 可以非常强大，因为嵌套数据意味着处理预先连接的表。但分析师在使用时会遇到困难……

towardsdatascience.com

似乎唯一一个沿着这种嵌套数据路线发展的引擎是[AWS Redshift Spectrum](https://docs.aws.amazon.com/redshift/latest/dg/nested-data-use-cases.html)。然而，如果我们想在另一个系统中使用 Google Analytics (GA) 数据，几乎总是需要将数据去连接（de-join），以便获得平面表格，因为聚合或修改结构数组的能力非常有限。大多数分析型数据库引擎似乎都在优化……
