# 使用 Python 和 SQL 自动化 ETL 到 SFTP 服务器

> 原文：[`towardsdatascience.com/automating-etl-to-sftp-server-using-python-and-sql-4e5c96e77937?source=collection_archive---------3-----------------------#2024-08-24`](https://towardsdatascience.com/automating-etl-to-sftp-server-using-python-and-sql-4e5c96e77937?source=collection_archive---------3-----------------------#2024-08-24)

## 学习如何在 Windows 上自动化每日数据传输过程，从 PostgreSQL 数据库到远程服务器

[](https://medium.com/@aramary?source=post_page---byline--4e5c96e77937--------------------------------)![Mary Ara](https://medium.com/@aramary?source=post_page---byline--4e5c96e77937--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4e5c96e77937--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4e5c96e77937--------------------------------) [Mary Ara](https://medium.com/@aramary?source=post_page---byline--4e5c96e77937--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4e5c96e77937--------------------------------) ·阅读时长 16 分钟·2024 年 8 月 24 日

--

![](img/e4239526be36bdd269d418f872c2a3cc.png)

图片由 [Shubham Dhage](https://unsplash.com/@theshubhamdhage?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

文件从一个位置传输到另一个位置的过程显然是自动化的完美候选者。这个过程反复执行可能会让人感到乏味，尤其是当你需要为多个数据组执行整个 ETL（提取、转换、加载）过程时。

想象一下，你所在的公司将数据存储在数据仓库中，然后他们决定将部分分析工作外包给一个外部数据分析供应商。该供应商提供一个定制的分析软件，能够为公司核心生产团队展示仪表板和报告。

这意味着，作为数据工程师的你，将需要按照外包合同的约定，定期（每天、每小时、每 30 分钟或其他频率）将数据传输给该供应商。

本文详细解释了包含 SFTP 上传的 ETL 过程。我们将结合使用安全文件传输协议（SFTP），这是一种通过加密文件来在两台远程服务器之间传输文件的安全方式，采用的加密方法是被称为安全外壳（SSH）协议。
