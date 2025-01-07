# PostgreSQL：仅为普通人优化查询

> 原文：[https://towardsdatascience.com/query-optimization-for-mere-humans-in-postgresql-875ab864390a?source=collection_archive---------4-----------------------#2024-12-03](https://towardsdatascience.com/query-optimization-for-mere-humans-in-postgresql-875ab864390a?source=collection_archive---------4-----------------------#2024-12-03)

## 理解 PostgreSQL 执行计划并通过实际示例讲解

[](https://medium.com/@Eyaltra?source=post_page---byline--875ab864390a--------------------------------)[![Eyal Trabelsi](../Images/60562caa76b824eac9e21f1c0a2933fc.png)](https://medium.com/@Eyaltra?source=post_page---byline--875ab864390a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--875ab864390a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--875ab864390a--------------------------------) [Eyal Trabelsi](https://medium.com/@Eyaltra?source=post_page---byline--875ab864390a--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--875ab864390a--------------------------------) ·阅读时间 8 分钟 ·2024年12月3日

--

![](../Images/4f384f53a80ad0fe12427dcc5b83524a.png)

图片来源：[Greg Rakozy](https://unsplash.com/@grakozy?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 于 [Unsplash](https://unsplash.com/photos/silhouette-photography-of-person-oMpAz-DN-9I?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

如今，用户对所使用的程序有很高的期望。用户希望程序具备惊人的功能，速度快，并且合理地消耗资源。

作为开发人员，我们应该努力为用户提供最佳的体验。数据库成为瓶颈是很常见的情况，优化查询并消除瓶颈并非易事。不幸的是，随着程序变得越来越复杂，数据量也越来越大，编写完美的 SQL 查询变得愈加困难。

今天，我将重点介绍一种通过使用 *Explain* 子句来找到瓶颈的技术。今天的目标是向你展示，找到并消除这些瓶颈并非难事，任何人都可以轻松发现瓶颈所在。

本文的代码可以在 [GitHub](https://github.com/eyaltrabelsi/my-notebooks/blob/master/Lectures/query_optimization_for_mere_humans/Query%20Optimization%20for%20Mere%20Humans.ipynb) 上找到。

注：所有图片，除非另有说明，均由作者提供。

# 让我们解释一下 Explain 📜

与数据库的交互是通过声明式语言进行的，其中 SQL 是最常用的语言。数据库决定了背后如何以及做什么，唯一提供的视角是执行计划。

这种限制使得在实践中实现合适的调试工具和性能分析器几乎不可能。**所以我们在执行计划上有点困住了。**

## 行业术语警告 🐝！！我的目标是让执行计划大众化。

在 PostgreSQL 中，要获取执行计划，应该使用 *Explain*/*Explain analyze* 子句：

+   *EXPLAIN* 显示了计划者打算做什么。

+   *EXPLAIN ANALYZE* 显示计划执行者的计划，**执行查询，** 并展示它是如何执行的。

**专家提示 #1**💃：在你的职业生涯中至少看一次执行计划。这在不同的数据库中类似，而且在公司中是一个稀缺的技能。

**专家提示 #2** 💃：更倾向于使用 *EXPLAIN ANALYZE*，因为它在大多数情况下包含更多信息。

**警告 #1** ⚠️ 不要在像 *DELETE/UPDATE* 这样的破坏性操作上使用 *EXPLAIN ANALYZE*，*EXPLAIN* 就足够了，它不会执行查询。

**警告 #2** ⚠️ 当资源紧张时（例如生产环境监控），不要使用 *EXPLAIN ANALYZE*，如果查询永远不会完成，*EXPLAIN* 就足够了，它不会执行查询。

*Explain* 是一个很棒的工具，因为它可以解释查询慢的原因，包括：

+   缺失/过度使用的索引/分区。

+   未优化的数据库配置。

+   冗余操作。

+   过时的统计信息。

+   过多的 I/O 操作。

对于更细致的人，你可以在下图中看到 *Explain* 子句的语法：

![](../Images/61acb4497b1c5ad8f1db4fcd7a3597b8.png)

PostgreSQL Explain 子句语法。

# 理解 Explain 解剖学🫀

我们将用它作为一个简单查询的示例：我们想计算没有 Twitter 句柄的用户数量。

```py
EXPLAIN ANALYZE
SELECT COUNT(*) FROM users WHERE twitter != '';
```

![](../Images/9a8f28ed38a9fe982ff8f92ce75497f5.png)

我们可以看到从 EXPLAIN ANALYZE 子句返回的执行计划。

起初它看起来很神秘，而且比我们的查询还长，甚至在一个小的实际执行计划示例中，如果不集中注意力，它会让人感到压倒。

**但它确实提供了有用的信息。** 我们可以看到查询执行花费了1.27秒，而查询计划只花费了0.4毫秒（可忽略的时间）。

![](../Images/d5e57f45b5c3501eb7d865b1efe9c8d4.png)

我们可以看到查询计划和执行所花费的时间。

执行计划的结构是一个反向树。在下图中，你可以看到执行计划被分成不同的节点，每个节点代表不同的操作，不管是 *聚合* 还是 *扫描*。

![](../Images/6a7ccef3d946eef093a5bca9a3c0c39d.png)

我们可以看到查询计划和执行所花费的时间。

有许多种节点操作，从与扫描相关的（*‘Seq Scan’，‘Index Only Scan’ 等等*），与连接相关的（*‘Hash Join’，‘Nested Loop’ 等等*），与聚合相关的（*‘GroupAggregate’，‘Aggregate’ 等等*）以及其他的（*‘Limit’，‘Sort’，‘materialize’ 等等*）。**幸运的是，你不需要记住这些。**

**专家提示 #3** 💃：集中注意力，专注于有问题的节点。

**专家提示 #4** 💃：作弊！在有问题的节点上搜索它们在 [explain 词汇表](https://www.pgmustard.com/docs/explain) 中的含义。

现在，让我们深入探讨如何识别出问题节点。

![](../Images/5765a24e17bb9ae9361b2481951954f0.png)

我们可以在每个节点中看到大量信息。

让我们深入了解这些度量指标的实际含义。

+   **实际循环数**：相同节点执行的循环次数为 1。为了获得总时间和行数，实际时间和行数需要乘以循环次数的值。

+   **实际行数**：*聚合* 节点生成的实际行数为 1（每次循环的平均值，循环次数为 1）。

+   **计划行数**：*聚合* 节点的估算生成行数为 1。行数的估算值可能会根据统计数据有所偏差。

+   **实际启动时间**：返回 *聚合* 节点第一行所花费的时间，单位为毫秒，为 1271.157（已聚合并包含之前的操作）。

+   **启动成本**：表示返回 *聚合* 节点第一行的估算时间的任意单位为 845110（已聚合并包含之前的操作）。

+   **实际总时间**：返回所有行所花费的时间（单位：毫秒）在 *聚合* 节点中为 1271.158（每次循环的平均值，循环次数为 1，已聚合并包含之前的操作）。

+   **总成本**：表示返回 *聚合* 节点所有行的估算时间的任意单位为 845110（已聚合）。

+   **计划宽度**：*聚合* 节点的估算行大小为 8 字节。

**专业小贴士 #5** 💃：注意循环，记住当关心 *实际行数* 和 *实际总时间* 时，要乘以循环次数。

我们将在下一节中讲解一个实际的例子。

# 示例：性能优化🐆

我们将使用与之前相同的查询。

```py
EXPLAIN ANALYZE
SELECT COUNT(*) FROM users WHERE twitter != '';
```

![](../Images/c9d940c226ab0b75dc4f477da4b96027.png)

我们将聚焦于实际时间最长的 Seq Scan 节点。

我们聚焦于最耗时的操作，即对用户表的[顺序扫描](https://www.pgmustard.com/docs/explain/sequential-scan)。该扫描筛选出了 2,487,813 行，占用了 1.27 秒（总时间为 1.271 秒）。

**但我们毕竟只是人类，这并没有给我们带来任何线索。**让我们用 Google 搜索一下（你也可以使用 ChatGPT）!!!。

![](../Images/e202c10e9f78a7914c4ec6e1224da7c4.png)

在 Google 上搜索如何加速 PostgreSQL 中的顺序扫描。

```py
CREATE INDEX twitter_test ON users (twitter)
```

![](../Images/2dbebcdb3d427e75d385fb1194604613.png)

我们的表现已经好多了，但“扫描”节点在实际时间上仍然是最慢的。

我们可以看到，现在我们在用户表上执行的是[仅索引扫描](https://www.pgmustard.com/docs/explain/index-only-scan)。它只用了 0.29 秒，而不是 1.27 秒，这非常棒，但对我们来说仍然不够。

**专业小贴士 #6**💃：一步一步地优化你的查询。

要理解传递给扫描的数据量，我们可以使用下方的缓存参数。

**专业小贴士 #7**💃：在比较执行计划时，关注多个度量指标。

```py
EXPLAIN (ANALYZE, BUFFERS)
SELECT COUNT(*) FROM users WHERE twitter != ''
```

![](../Images/93d19daed446727c8e7c2a33d3a8c6c9.png)

我们可以看到很多关于数据命中缓存或磁盘的信息。

我们有51,854页需要从缓存中读取（400 MB），因此改进配置可能不会带来剧烈变化。

但是，我们并非没有选择。由于扫描排除了2,487,813行数据，我们可以将索引更改为部分索引，但这并不是免费的。这会导致写入时间变长，并且需要额外的存储，这对垂直扩展的系统影响较大。

**专业提示 #8** 💃：世上没有免费的午餐。

# 良好的优化选项🤞🏻

我不会深入讲解太多细节，因为这篇博客已经相当长了。这些是当你遇到慢查询时，可能首先需要解决的问题：

+   选择正确的扫描方法。

+   选择正确的连接方法。

+   选择正确的连接顺序。

+   尽早推送过滤器。

+   在需要时减少磁盘IO操作。

为了手动检查特定的优化，可以启用/禁用一些设置。

```py
SET enable_seqscan TO off;
EXPLAIN (ANALYZE) SELECT * FROM foo WHERE c1 > 500;
SET enable_seqscan TO on;
```

**警告 #3** ⚠️：只有在你尝试过最基本的优化之后，才可以启用/禁用设置，因为大多数情况下，PostgreSQL知道自己在做什么。

# 难道没有更简单的方法吗？！🙏

不幸的是，*Explain*并不完美，且有一些原因导致它并不在每个开发者的工具箱中：

**你没有生产环境中所有执行计划的历史记录。**

我们可以通过使用像[auto_explain](https://www.postgresql.org/docs/current/auto-explain.html)和[pg_stat_plans](https://github.com/2ndQuadrant/pg_stat_plans)这样的工具来记录在特定条件下的执行计划，从而弥补缺乏历史记录的问题，且它们不会对生产环境造成重大影响。另一种方法是记录哪些查询在何时运行，并尝试重现它，但这比看起来更复杂。

**调优复杂查询并非一项简单任务，尤其是使用“裸执行计划”**，因为它们通常很长且难以阅读。此外，它们往往无法提供为何某个特定优化未被利用的洞察，也不能提供如何有效重写查询以提升性能的指导。

然而，这一挑战可以通过采用高度特定结构和元数据，并使用专业工具来解决。**这些工具帮助你/LLM专注于重要事项**（无论是架构、瓶颈等）。一些最著名的工具包括：

+   [eversql](https://www.eversql.com/) — 一款成熟的解决方案，旨在为你的*PostgreSQL*查询提供变更建议。

+   [metis](https://www.metisdata.io/) — 旨在为你的*PostgreSQL*数据库提供变更建议，作为开发和CI/CD过程中的一项防护机制。

+   [QueryFlow](https://github.com/eyaltrabelsi/query-flow) — 一款开源工具，允许识别多个查询的错误和性能调优（因为最难调试的查询在孤立时表现得很平稳）。

**专业提示 #9** 💃：使用工具让你的生活更轻松。

我将给你展示使用像QueryFlow这样的工具有多方便（更多细节可以阅读下面的内容）。

![](../Images/8b7368c085df0fdc6607ce2201a3a0b9.png)

QueryFlow 执行计划的可视化，专注于持续时间

应该很容易看出，*仅索引扫描*的宽度远大于聚合宽度，这表明我们应该将重点放在这里。在多个复杂查询中，其他工具往往缺乏这种能力。

# 最后的话

在本文中，我们回顾了一些最常见的原因，这些原因可能导致本来完全正常的 SQL 在任何对时间敏感的应用中变得过于缓慢，并**介绍了一种神话般的方法来识别并避免这些问题**。

由于话题的广泛性，有许多优化措施我没有涵盖。因此，如果你想更深入了解，我在最后添加了额外的资源。

我对未来充满信心。我相信这些工具将变得和 [*在 Python 中打开文件*](https://www.w3schools.com/python/python_file_open.asp)一样简单，无论是通过集成到 IDE 和客户端，还是提供 SAS 解决方案。这将使我们能够变得更加主动，而不是被动应对。

我希望我能够传达我对这个迷人话题的热情，并且你觉得这些内容对你有帮助，像往常一样，我愿意接受任何建设性的反馈。

# 附加资源 📚

+   [PostgreSQL 执行计划的更深入理解（视频）](https://www.youtube.com/watch?v=Ls-uE1V31lE&list=WL&index=5&ab_channel=PostgresConference)

+   [EXPLAIN 解析（视频）](https://www.youtube.com/watch?v=mCwwFAl1pBU&ab_channel=SouthernCaliforniaLinuxExpo)

+   [理解 EXPLAIN](https://public.dalibo.com/exports/conferences/_archives/_2012/201211_explain/understanding_explain.pdf)
