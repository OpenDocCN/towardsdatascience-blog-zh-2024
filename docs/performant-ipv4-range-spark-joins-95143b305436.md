# 高性能IPv4范围Spark连接

> 原文：[https://towardsdatascience.com/performant-ipv4-range-spark-joins-95143b305436?source=collection_archive---------3-----------------------#2024-01-25](https://towardsdatascience.com/performant-ipv4-range-spark-joins-95143b305436?source=collection_archive---------3-----------------------#2024-01-25)

## 实用指南：优化Spark中的非等值连接

[](https://medium.com/@jean-claude.cote?source=post_page---byline--95143b305436--------------------------------)[![Jean-Claude Cote](../Images/aea2df9c7b95fc85cc336f64d64b0a76.png)](https://medium.com/@jean-claude.cote?source=post_page---byline--95143b305436--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--95143b305436--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--95143b305436--------------------------------) [Jean-Claude Cote](https://medium.com/@jean-claude.cote?source=post_page---byline--95143b305436--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--95143b305436--------------------------------) ·9分钟阅读·2024年1月25日

--

![](../Images/e2dd3d2557024e0fd123571495dea461.png)

图片由John Lee提供，来源于Unsplash

通过IP地理位置信息丰富网络事件是一个至关重要的任务，特别是对于[加拿大网络安全中心](https://www.cyber.gc.ca/en)等组织，这是加拿大的国家计算机安全事件响应团队（CSIRT）。在本文中，我们将展示如何优化Spark SQL连接，特别关注涉及不等式条件的场景——这是处理IP地理位置数据时常见的挑战。

作为网络安全从业者，我们依赖于通过IP地理位置数据库丰富网络事件，这就要求我们采用高效的策略来处理非等值连接。尽管有许多文章阐述了Spark支持的各种连接策略，但这些策略在实际应用中的效果仍然是业内专业人士关注的一个问题。

David Vrba的深刻文章，[“关于Spark 3.0中的连接”](/about-joins-in-spark-3-0-1e0ea083ea86)，发布于Towards Data Science，是一篇宝贵的资源。它解释了Spark选择特定连接策略的条件。在他的文章中，David简要地指出，优化非等值连接的方法是将其转化为等值连接。

本文旨在提供一个实用指南，帮助优化非等值连接的性能，特别是聚焦于与地理位置表中的IP范围进行连接的情况。

为了举例说明这些优化，我们将回顾在[我们之前的文章](/unleashing-the-power-of-sql-analytical-window-functions-a-deep-dive-into-fusing-ipv4-blocks-62bf2b3405e0)中介绍的地理位置表。

```py
+----------+--------+---------+-----------+-----------+
| start_ip | end_ip | country |    city   |   owner   |
+----------+--------+---------+-----------+-----------+
| 1        | 2      | ca      | Toronto   | Telus     |
| 3        | 4      | ca      | Quebec    | Rogers    |
| 5        | 8      | ca      | Vancouver | Bell      |
| 10       | 14     | ca      | Montreal  | Telus     |
| 19       | 22     | ca      | Ottawa    | Rogers    |
| 23       | 29     | ca      | Calgary   | Videotron |
+----------+--------+---------+-----------+-----------+
```

## 等值连接

为了说明 Spark 如何执行等值连接，我们将通过考虑一个假设的场景来开始我们的探索。假设我们有一个事件表，每个事件都与特定的 `owner` 相关联，该 `owner` 由 `event_owner` 列表示。

```py
+------------+--------------+
| event_time | event_owner  |
+------------+--------------+
| 2024-01-01 | Telus        |
| 2024-01-02 | Bell         |
| 2024-01-03 | Rogers       |
| 2024-01-04 | Videotron    |
| 2024-01-05 | Telus        |
| 2024-01-06 | Videotron    |
| 2024-01-07 | Rogers       |
| 2024-01-08 | Bell         |
+------------+--------------+
```

让我们更仔细地看看 Spark 如何处理这个等值连接：

```py
SELECT
  *
FROM
  events
  JOIN geolocation
  ON (event_owner = owner)
```

在这个例子中，等值连接建立在 `events` 表和 `geolocation` 表之间。连接的标准是基于 `events` 表中的 `event_owner` 列与 `geolocation` 表中的 `owner` 列是否相等。

正如 David Vrba 在他的博客中所解释的：

> 如果存在等值条件，并且连接键是可排序的，Spark 会选择使用 SMJ 进行连接规划。

Spark 将执行排序合并连接（Sort Merge Join），通过对左侧的 `event_owner` 和右侧的 `owner` 进行哈希操作来分配两表的行。哈希到同一 Spark 分区的两表行将由同一 Spark 任务处理——一个工作单元。例如，Task-1 可能接收：

```py
+----------+-------+---------+-----------+-----------+
| start_ip | end_ip| country |   city    |   owner   |
+----------+-------+---------+-----------+-----------+
| 1        | 2     | ca      | Toronto   | Telus     |
| 10       | 14    | ca      | Montreal  | Telus     |
+----------+-------+---------+-----------+-----------+

+------------+--------------+
| event_time | event_owner  |
+------------+--------------+
| 2024-01-01 | Telus        |
| 2024-01-05 | Telus        |
+------------+--------------+
```

注意 Task-1 仅处理数据的一个子集。连接问题被划分为多个较小的任务，其中只需要左右两边的部分行。此外，Task-1 处理的左右两边行必须匹配。因为无论来自 `events` 表还是 `geolocation` 表，“Telus” 的每次出现都会哈希到相同的分区。我们可以确定其他 Task-X 不会有 `owner` 为“Telus”的行。

一旦数据按照上面所示的方式划分，Spark 将对两边的数据进行排序，因此这种连接策略被称为排序合并连接（Sort Merge Join）。合并操作通过取左边的第一行并测试其是否与右边匹配来进行。一旦右边的行不再匹配，Spark 将从左边提取行。它将不断从两边队列中提取，直到两边没有剩余行。

## 非等值连接

现在我们更好地理解了等值连接是如何执行的，接下来我们将其与非等值连接进行对比。假设我们有事件表，其中包含 `event_ip`，并且我们希望向该表添加地理位置信息。

```py
+------------+----------+
| event_time | event_ip |
+------------+----------+
| 2024-01-01 |    6     |
| 2024-01-02 |   14     |
| 2024-01-03 |   18     |
| 2024-01-04 |   27     |
| 2024-01-05 |    9     |
| 2024-01-06 |   23     |
| 2024-01-07 |   15     |
| 2024-01-08 |    1     |
+------------+----------+
```

为了执行此连接，我们需要确定 `event_ip` 所在的 IP 范围。我们通过以下条件来实现这一点：

```py
SELECT
  *
FROM
  events
  JOIN geolocation
  ON (event_ip >= start_ip and event_ip <= end_ip)
```

现在，让我们考虑 Spark 如何执行此连接。在右边（地理位置表），没有可以哈希并分配行的键。无法将此问题划分为可以在计算集群中分发并并行执行的较小任务。

在这种情况下，Spark 被迫采用更为资源密集型的连接策略。正如 David Vrba 所说：

> 如果没有等值条件，Spark 必须使用广播嵌套循环连接（BroadcastNestedLoopJoin，BNLJ）或笛卡尔积连接（Cartesian Product Join，CPJ）。

这两种策略都涉及暴力破解问题；对于左侧的每一行，Spark会对右侧的每一行测试“between”条件。它别无选择。如果右侧的表足够小，Spark可以通过将右侧表复制到每个读取左侧的任务中来优化，这种情况称为BNLJ（嵌套循环连接）情况。然而，如果左侧太大，每个任务将需要读取右侧和左侧的表，这种情况称为CPJ（连接点连接）情况。在这两种情况下，这两种策略都是非常昂贵的。

那么，我们如何改进这种情况呢？诀窍是引入连接条件中的相等性。例如，我们可以简单地展开地理位置表中的所有IP范围，为每个IP生成一行。

这是在Spark中容易实现的；我们可以执行以下SQL来展开所有IP范围：

```py
SELECT
  country,
  city,
  owner,
  explode(sequence(start_ip, end_ip)) AS ip
FROM
  geolocation
```

[sequence](https://spark.apache.org/docs/latest/api/sql/#sequence)函数创建一个从`start_ip`到`end_ip`的IP值数组。[explode](https://spark.apache.org/docs/latest/api/sql/#explode)函数将这个数组展开成单独的行。

```py
+---------+---------+---------+-----------+
| country |   city    |  owner    |   ip   |
+---------+---------+---------+-----------+
|   ca    | Toronto   |  Telus    |   1    |
|   ca    | Toronto   |  Telus    |   2    |
|   ca    | Quebec    |  Rogers   |   3    |
|   ca    | Quebec    |  Rogers   |   4    |
|   ca    | Vancouver |  Bell     |   5    |
|   ca    | Vancouver |  Bell     |   6    |
|   ca    | Vancouver |  Bell     |   7    |
|   ca    | Vancouver |  Bell     |   8    |
|   ca    | Montreal  |  Telus    |  10    |
|   ca    | Montreal  |  Telus    |  11    |
|   ca    | Montreal  |  Telus    |  12    |
|   ca    | Montreal  |  Telus    |  13    |
|   ca    | Montreal  |  Telus    |  14    |
|   ca    | Ottawa    |  Rogers   |  19    |
|   ca    | Ottawa    |  Rogers   |  20    |
|   ca    | Ottawa    |  Rogers   |  21    |
|   ca    | Ottawa    |  Rogers   |  22    |
|   ca    | Calgary   | Videotron |  23    |
|   ca    | Calgary   | Videotron |  24    |
|   ca    | Calgary   | Videotron |  25    |
|   ca    | Calgary   | Videotron |  26    |
|   ca    | Calgary   | Videotron |  27    |
|   ca    | Calgary   | Videotron |  28    |
|   ca    | Calgary   | Videotron |  29    |
+---------+---------+---------+-----------+
```

有了两侧的键，我们现在可以执行等值连接，Spark可以高效地分配问题，从而实现最优性能。然而，在实际应用中，这种情况并不现实，因为真正的地理位置表通常包含数十亿行。

为了解决这个问题，我们可以通过增加映射的粗糙度来提高效率。我们可以将IP范围映射到IP空间中的段，而不是将IP范围映射到每个单独的IP。假设我们将IP空间分割成5个一组的段。分段后的空间大致如下所示：

```py
+---------------+-------------+-----------+
| segment_start | segment_end | bucket_id |
+---------------+-------------+-----------+
|       1       |      5      |     0     |
|       6       |     10      |     1     |
|      11       |     15      |     2     |
|      16       |     20      |     3     |
|      21       |     25      |     4     |
|      26       |     30      |     5     |
+---------------+-------------+-----------+
```

现在，我们的目标是将IP范围映射到它们重叠的段。类似于我们之前做的那样，我们可以展开IP范围，但这次我们将按5个一组的段进行操作。

```py
SELECT
  country,
  city,
  owner,
  explode(sequence(start_ip / 5, end_ip / 5)) AS bucket_id
FROM
  geolocations
```

我们观察到某些IP范围共享相同的`bucket_id`。范围1–2和3–4都属于段1–5。

```py
+----------+--------+---------+-----------+-----------+-----------+
| start_ip | end_ip | country |    city   |   owner   | bucket_id |
+----------+--------+---------+-----------+-----------+-----------+
|    1     |    2   |   ca    | Toronto   |   Telus   |     0     |
|    3     |    4   |   ca    | Quebec    |  Rogers   |     0     |
|    5     |    8   |   ca    | Vancouver |   Bell    |     1     |
|   10     |   14   |   ca    | Montreal  |   Telus   |     2     |
|   19     |   22   |   ca    |  Ottawa   |  Rogers   |     3     |
|   19     |   22   |   ca    |  Ottawa   |  Rogers   |     4     |
|   23     |   29   |   ca    | Calgary   | Videotron |     4     |
|   23     |   29   |   ca    | Calgary   | Videotron |     5     |
+----------+--------+---------+-----------+-----------+-----------+
```

此外，我们注意到一些IP范围是重复的。IP范围23–29的最后两行与段20–25和26–30重叠。类似于我们展开单个IP的情况，我们仍然在重复行，但重复的程度要小得多。

现在，我们可以利用这个分桶表来执行我们的连接。

```py
SELECT
  *
FROM
  events
  JOIN geolocation
  ON (
    event_ip / 5 = bucket_id
    AND event_ip >= start_ip
    AND event_ip <= end_ip
  )
```

连接中的相等条件使得Spark能够执行排序合并连接（Sort Merge Join，SMJ）策略。“between”条件消除了IP范围共享相同`bucket_id`的情况。

在这个示例中，我们使用了5个一组的段；然而，实际上，我们会将IP空间分割成256个一组的段。这是因为全球IP地址空间由互联网号码分配局（IANA）监管，传统上，IANA按256个IP的块分配地址空间。

使用Spark的`approx_percentile`函数分析真正的地理位置表中的IP范围可以发现，大多数记录的跨度小于256，而大于256的记录非常少。

```py
SELECT 
    approx_percentile(
        end_ip - start_ip, 
        array(0.800, 0.900, 0.950, 0.990, 0.999, 0.9999),
        10000)
FROM
    geolocation
```

这意味着大多数 IP 范围都被分配了一个`bucket_id`，而少数较大的范围则被展开，导致展开后的表格大约包含额外 10% 的行。

执行的查询与一个真实的地理定位表可能如下所示：

```py
WITH
  b_geo AS (
    SELECT
      explode(
        sequence(
          CAST(start_ip / 256 AS INT), 
          CAST(end_ip / 256 AS INT))) AS bucket_id,
      *
    FROM
      geolocation
  ),
  b_events AS (
    SELECT
      CAST(event_ip / 256 AS INT) AS bucket_id,
      *
    FROM
      events
  )

SELECT
  *
FROM
  b_events
  JOIN b_geo
  ON (
    b_events.bucket_id = b_geo.bucket_id
    AND b_events.event_ip >= b_geo.start_ip
    AND b_events.event_ip <= b_geo.end_ip
  );
```

## 结论

总之，本文展示了通过实现一种涉及分割 IP 范围的映射技术，将非等值连接转化为等值连接的实际示例。需要特别注意的是，这种方法不仅限于 IP 地址，还可以应用于任何由带区或范围构成的数据集。

有效地映射和分割数据是数据工程师和分析师工具箱中一个有价值的工具，为 Spark SQL 连接中的非等值条件所带来的挑战提供了实际的解决方案。
