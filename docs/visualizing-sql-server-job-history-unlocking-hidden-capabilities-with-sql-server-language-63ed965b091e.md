# 可视化 SQL Server 作业历史：利用 SQL Server 语言扩展解锁隐藏功能

> 原文：[https://towardsdatascience.com/visualizing-sql-server-job-history-unlocking-hidden-capabilities-with-sql-server-language-63ed965b091e?source=collection_archive---------3-----------------------#2024-05-21](https://towardsdatascience.com/visualizing-sql-server-job-history-unlocking-hidden-capabilities-with-sql-server-language-63ed965b091e?source=collection_archive---------3-----------------------#2024-05-21)

## 轻松通过用户友好的互动时间线可视化来导航和分析作业工作负载

[](https://lucazavarella.medium.com/?source=post_page---byline--63ed965b091e--------------------------------)[![Luca Zavarella](../Images/6af72e50d79498ac378e2f1e469a0e65.png)](https://lucazavarella.medium.com/?source=post_page---byline--63ed965b091e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--63ed965b091e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--63ed965b091e--------------------------------) [Luca Zavarella](https://lucazavarella.medium.com/?source=post_page---byline--63ed965b091e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--63ed965b091e--------------------------------) ·18 分钟阅读·2024年5月21日

--

![](../Images/32717005f5aaf99ecedf5aa2c620170d.png)

（图片由 ChatGPT 提供）

在任何企业中，数据在决策中发挥着关键作用。越来越多的情况下，这需要将原始数据转化为有用的信息。这些过程是企业内部商业智能过程的关键组成部分。鉴于其复杂性和显著的时间要求，必须合理安排这些数据转换活动的时间，以最大化效率和资源利用率。

这些活动通常在较大的组织中自动化并组织为作业，尤其是在数据操作庞大且多样的情况下。每个作业消耗不同数量的时间和资源。随着这些任务量的增加，挑战在于如何在 24 小时内优化调度它们。调度至关重要，不仅是为了保持操作效率，还要确保两个大型作业不会同时运行，从而导致系统过载或变慢。

数据库管理员（DBA）配备了提取详细作业执行日志的工具。这些日志提供了关于如何…
