# DuckDB 和 AWS — 如何在 1 分钟内聚合 1 亿条数据

> 原文：[https://towardsdatascience.com/duckdb-and-aws-how-to-aggregate-100-million-rows-in-1-minute-3634eef06b79?source=collection_archive---------1-----------------------#2024-04-25](https://towardsdatascience.com/duckdb-and-aws-how-to-aggregate-100-million-rows-in-1-minute-3634eef06b79?source=collection_archive---------1-----------------------#2024-04-25)

## 使用 Python 和 DuckDB 处理大规模数据 — 一个 AWS S3 示例。

[](https://medium.com/@radecicdario?source=post_page---byline--3634eef06b79--------------------------------)[![Dario Radečić](../Images/41882a3b30bab9da43d66a59f1df366b.png)](https://medium.com/@radecicdario?source=post_page---byline--3634eef06b79--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3634eef06b79--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3634eef06b79--------------------------------) [Dario Radečić](https://medium.com/@radecicdario?source=post_page---byline--3634eef06b79--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3634eef06b79--------------------------------) ·阅读时长 4 分钟·2024 年 4 月 25 日

--

![](../Images/4a7174c3f10ec72b94d874017fabb6bb.png)

图片来源：[Growtika](https://unsplash.com/@growtika?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

当公司需要一个安全、高效且可扩展的存储解决方案时，它们通常会选择云服务。AWS S3 是行业中最受欢迎的平台之一 — 理由也很充分 — 它是行业领先的对象存储解决方案，可以作为数据湖使用。

问题是 — **你能在不下载 S3 存储桶数据的情况下聚合它吗？而且你能快速完成吗？**

答案是**肯定的**，两个问题的答案都是肯定的。DuckDB 允许你通过 `httpfs` 扩展直接连接到 S3 存储桶。今天你将通过聚合大约 1.11 亿条数据，这些数据分布在 37 个[Parquet](/csv-files-for-storage-no-thanks-theres-a-better-option-72c78a414d1d) 文件中，来学习如何使用它。

**剧透：** 你大约只需 1 分钟。

*注意：* 我写这篇文章是因为我在寻找一个更高效的 Pandas 替代品。我的目标是在本地对大规模数据集进行分析，而不是选择云解决方案。我与 DuckDB 或 AWS 没有任何关联。

# AWS S3 设置

首先，你需要一个 AWS 账户和一个 S3 存储桶。你还需要创建一个 IAM 用户，并为其生成访问密钥。
