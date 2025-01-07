# 发掘 SQL 分析窗口函数的威力：深入探讨融合 IPv4 块

> 原文：[`towardsdatascience.com/unleashing-the-power-of-sql-analytical-window-functions-a-deep-dive-into-fusing-ipv4-blocks-62bf2b3405e0?source=collection_archive---------15-----------------------#2024-01-10`](https://towardsdatascience.com/unleashing-the-power-of-sql-analytical-window-functions-a-deep-dive-into-fusing-ipv4-blocks-62bf2b3405e0?source=collection_archive---------15-----------------------#2024-01-10)

## 如何通过合并连续的网络 IPv4 块来总结地理位置表

[](https://medium.com/@jean-claude.cote?source=post_page---byline--62bf2b3405e0--------------------------------)![Jean-Claude Cote](https://medium.com/@jean-claude.cote?source=post_page---byline--62bf2b3405e0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--62bf2b3405e0--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--62bf2b3405e0--------------------------------) [Jean-Claude Cote](https://medium.com/@jean-claude.cote?source=post_page---byline--62bf2b3405e0--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--62bf2b3405e0--------------------------------) ·阅读时长 8 分钟·2024 年 1 月 10 日

--

![](img/e46edade3e88927aa214a1e67a73ca08.png)

图片来源：Pascal Bernardon，来自 Unsplash

互联网上的每个设备都有一个唯一的 Internet Protocol（IP）地址。全球的 IP 地址空间由互联网号码分配局（IANA）管理。传统上，IANA 为 IPv4 分配 /8 前缀块，随后这些地址块被分配给互联网服务提供商和其他组织。现有多种数据库将这些 IP 块与其各自的所有者进行映射，并提供有关来源国家和城市的信息。

作为加拿大国家计算机安全事件响应团队（CSIRT），我们，[加拿大网络安全中心](https://cyber.gc.ca/en)，在查询给定的 IP 或通过 SQL JOIN 操作增强整个数据集时，通常会依赖这些数据库。然而，并非所有的使用场景都需要精确到城市级别；有时候，仅仅知道国家信息就足够了。

在一个国家内，许多网络块是连续的。将这些块合并成超大块可以显著减少将超大块与国家进行映射的表格大小，从而提升 JOIN 操作的效率。

在本文中，我们将演示如何通过合并连续的网络块来总结地理位置表。

假设我们的地理位置表包含以下数据：

```py
+----------+-------+---------+-----------+-----------+
| start_ip | end_ip| country |   city    |   owner   |
+----------+-------+---------+-----------+-----------+
| 1        | 2     | ca      | Toronto   | Telus     |
| 3        | 4     | ca      | Quebec    | Rogers    |
| 5        | 8     | ca      | Vancouver | Bell      |
| 10       | 14    | ca      | Montreal  | Telus     |
| 19       | 22    | ca      | Ottawa    | Rogers    |
| 23       | 29    | ca      | Calgary   | Videotron |
+----------+-------+---------+-----------+-----------+
```

这里，`start_ip`表示网络块中的最低值，`end_ip`表示最大值。通常，这些数字要大得多。例如，Google 的 DNS 服务器 8.8.8.8 的表示数字是 134744072。我们使用简单的合成值来进行说明。

首先，让我们进行一个简单的汇总。例如，统计每个国家分配的 IP 地址数量。这可以通过按国家分组数据，并对每个网络块的 IP 数量进行求和来实现。

```py
SELECT
    country,
    SUM(end_ip - start_ip + 1) as num_ip
FROM
    geo_table
GROUP BY
    country
```

这个语句按国家对行进行分组，并应用`SUM`聚合函数，计算每个国家的 IP 总数。需要注意的是，`SUM`聚合是结合性的，这意味着求和的顺序不重要，类似于数学中的加法。

```py
+---------+--------+
| country | num_ip |
+---------+--------+
| ca      | 24     |
+---------+--------+
```

现在，让我们深入探讨聚合连续网络块的复杂性。参照我们原始的表格，我们需要将前 3 行合并。区块 1-2、3-4、5-8 应合并为大区块 1-8。我们还需要合并最后两行。区块 19-22 和 23-29 应合并为 19-29。我们的目标是生成如下表格：

```py
+----------+-------+---------+
| start_ip | end_ip| country |
+----------+-------+---------+
| 1        | 8     | ca      |
| 10       | 14    | ca      |
| 19       | 29    | ca      |
+----------+-------+---------+
```

检测连续区块需要行间信息，并且行的顺序变得至关重要。幸运的是，窗口分析函数提供了解决方案，提供了一个行间引用的机制。这些函数，如`LEAD`和`LAG`，使得可以与前后行的值进行比较，从而帮助识别连续的 IP 块。

让我们将`LEAD`和`LAG`窗口函数应用到我们的表格中。注意，在`OVER`子句中，我们仍然指定数据按国家分组`PARTITION BY country`，同时还指定了数据的排序`ORDER BY start_ip`。

```py
SELECT
    *,
    LAG(end_ip) OVER (
      PARTITION BY country 
      ORDER BY start_ip) AS prev_end_ip,
    LEAD(start_ip) OVER (
      PARTITION BY country 
      ORDER BY start_ip) AS next_start_ip
FROM
    geo_table
```

结果表格`view_1`如下：

```py
+----------+-------+---------+-------------+---------------+
| start_ip | end_ip| country | prev_end_ip | next_start_ip |
+----------+-------+---------+-------------+---------------+
| 1        | 2     | ca      | null        | 3             |
| 3        | 4     | ca      | 2           | 5             |
| 5        | 8     | ca      | 4           | 10            |
| 10       | 14    | ca      | 8           | 19            |
| 19       | 22    | ca      | 14          | 23            |
| 23       | 29    | ca      | 22          | null          |
+----------+-------+---------+-------------+---------------+
```

区分窗口函数和简单的`GROUP BY`函数至关重要。在`OVER()`操作中，`LEAD`和`LAG`的结果会附加到每一行，为前后行的信息提供上下文。这与`GROUP BY`子句中的函数不同，后者将一组行减少为单一的聚合结果。

现在，我们可以访问前后行的详细信息，从而可以进行行间比较。这种比较对于识别连续 IP 块至关重要，帮助我们确定何时将相邻的块合并在一起。

每行可以处于四种状态之一：

1) 删除：该区块与前一个和下一个区块都相连。

2) 起始：该区块只与下一个区块相连。

3) 结束：该区块只与前一个区块相连。

4) 保留：该区块既不与前一个区块也不与下一个区块相连。

让我们将这个`state`列添加到我们的表格中。

```py
SELECT
    *,
    CASE
        WHEN (end_ip = next_start_ip - 1) 
           AND (start_ip = prev_end_ip + 1) THEN 'remove'
        WHEN (end_ip = next_start_ip - 1) THEN 'start'
        WHEN (start_ip = prev_end_ip + 1) THEN 'end'
        ELSE 'keep'
    END AS state
FROM
    view_1
```

我们得到如下的`view_2`结果：

```py
+----------+-------+---------+-------------+---------------+-------+
| start_ip | end_ip| country | prev_end_ip | next_start_ip | state |
+----------+-------+---------+-------------+---------------+-------+
| 1        | 2     | ca      | null        | 3             | start |
| 3        | 4     | ca      | 2           | 5             | remove|
| 5        | 8     | ca      | 4           | 10            | end   |
| 10       | 14    | ca      | 8           | 19            | keep  |
| 19       | 22    | ca      | 14          | 23            | start |
| 23       | 29    | ca      | 22          | null          | end   |
+----------+-------+---------+-------------+---------------+-------+
```

我们可以继续删除位于起始和结束区块之间的行，特别是那些被标记为`remove`状态的行。

```py
SELECT
    start_ip,
    end_ip,
    country,
    state
FROM
    view_2
WHERE
    state IN ('start', 'end', 'keep')
```

结果为`view_3`：

```py
+----------+-------+---------+-------+
| start_ip | end_ip| country | state |
+----------+-------+---------+-------+
| 1        | 2     | ca      | start |
| 5        | 8     | ca      | end   |
| 10       | 14    | ca      | keep  |
| 19       | 22    | ca      | start |
| 23       | 29    | ca      | end   |
+----------+-------+---------+-------+
```

我们离目标越来越近了！现在我们只需要合并包含我们希望生成的巨型块的`start_ip`和`end_ip`的`start`行和`end`行。为了实现这一点，我们再次使用窗口函数。这次是从`end`行获取`end_ip`。

```py
SELECT
    *,
    LEAD(end_ip) OVER (
      PARTITION BY country 
      ORDER BY start_ip) AS next_end_ip
FROM
    view_3
```

在`view_4`中的结果：

```py
+----------+-------+---------+-------+-------------+
| start_ip | end_ip| country | state | next_end_ip |
+----------+-------+---------+-------+-------------+
| 1        | 2     | ca      | start | 8           |
| 5        | 8     | ca      | end   | 14          |
| 10       | 14    | ca      | keep  | 22          |
| 19       | 22    | ca      | start | 29          |
| 23       | 29    | ca      | end   | null        |
+----------+-------+---------+-------+-------------+
```

注意，状态为`start`的行现在具有`start_ip`和`next_end_ip`，这是构建巨型块所必需的信息。

状态为`end`的行不再需要，可以删除。

状态为`keep`的行已经具有正确的`end_ip`。

我们现在可以确定巨型块的`final_end`值。可能有两种情况：

1) 对于`start`行，我们从`next_end_ip`获取结束值。

2) 对于`keep`行，我们直接使用原始的`end_ip`值。

```py
SELECT
    start_ip AS final_start,
    CASE
        WHEN (state = 'start') THEN next_end_ip
        WHEN (state = 'keep') THEN end_ip
        ELSE NULL
    END AS final_end_ip
FROM
    view_4
WHERE
    state IN ('start', 'keep')
```

因此，我们达到了将连续 IPv4 块合并为巨型块的目标。

```py
+----------+-------+---------+-------+-------------+------------+----------+
| start_ip | end_ip| country | state | next_end_ip | final_start| final_end|
+----------+-------+---------+-------+-------------+------------+----------+
| 1        | 2     | ca      | start | 8           | 1          | 8        |
| 10       | 14    | ca      | keep  | 22          | 10         | 14       |
| 19       | 22    | ca      | start | 29          | 19         | 29       |
+----------+-------+---------+-------+-------------+------------+----------+
```

将上述所有语句结合起来，我们得到了一个最终的语句：

```py
SELECT
    country,
    final_start,
    IF(state = 'start', next_end_ip, final_end) AS final_end
FROM (
    SELECT
        country,
        start_ip AS final_start,
        end_ip AS final_end,
        LEAD(end_ip) OVER (
          PARTITION BY country 
          ORDER BY start_ip) AS next_end_ip
    FROM (
        SELECT
            start_ip,
            end_ip,
            country,
            CASE
                WHEN (end_ip = next_start_ip - 1) 
                  AND (start_ip = prev_end_ip + 1) THEN 'remove'
                WHEN (end_ip = next_start_ip - 1) THEN 'start'
                WHEN (start_ip = prev_end_ip + 1) THEN 'end'
                ELSE 'keep'
            END AS state
        FROM (
            SELECT
                *,
                LAG(end_ip) OVER (
                    PARTITION BY country 
                    ORDER BY start_ip) AS prev_end_ip,
                LEAD(start_ip) OVER (
                    PARTITION BY country 
                    ORDER BY start_ip) AS next_start_ip
            FROM 
                geo_table
        )
        WHERE 
            state IN ('start', 'end', 'keep')
    )
)
WHERE 
    state IN ('start', 'keep')
```

总结来说，SQL 分析窗口函数为复杂的数据分析提供了一个强大的框架。它们使得用户能够在保留每行上下文的同时执行聚合操作，从而便于完成诸如运行总和、平均值和百分位计算等任务。此外，这些函数在排名、时间序列分析以及检测数据集中的异常值和离群值中也起着至关重要的作用。这些函数是数据工作者工具包中不可或缺的资产。

分析窗口函数非常强大。在本文中，我们仅仅触及了其表面；例如，我们没有使用`window_frame`。窗口框架允许你进一步细化哪些行将被考虑在聚合中。窗口框架是相对于当前行的，并且可以基于行数或时间间隔，这使得这些函数在各种分析中不可或缺。你可以在 Spark 文档中了解更多关于这些功能的信息：[Spark SQL — Window Operations](https://spark.apache.org/docs/latest/sql-ref-syntax-qry-select-window.html)。
