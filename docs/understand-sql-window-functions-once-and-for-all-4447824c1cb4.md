# 一劳永逸地理解 SQL 窗口函数

> 原文：[https://towardsdatascience.com/understand-sql-window-functions-once-and-for-all-4447824c1cb4?source=collection_archive---------1-----------------------#2024-05-01](https://towardsdatascience.com/understand-sql-window-functions-once-and-for-all-4447824c1cb4?source=collection_archive---------1-----------------------#2024-05-01)

## 理解窗口函数的逐步指南

[](https://medium.com/@mtrentz?source=post_page---byline--4447824c1cb4--------------------------------)[![Mateus Trentz](../Images/8c50ef2bc9726b11094d6fa630015554.png)](https://medium.com/@mtrentz?source=post_page---byline--4447824c1cb4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4447824c1cb4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4447824c1cb4--------------------------------) [Mateus Trentz](https://medium.com/@mtrentz?source=post_page---byline--4447824c1cb4--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4447824c1cb4--------------------------------) ·阅读时间：9分钟·2024年5月1日

--

![](../Images/4d7e65d387759934b9acf4a61ec24a33.png)

图片来自[Yasmina H](https://unsplash.com/@yasmina?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)上的发布

窗口函数是编写既高效又易于理解的 SQL 代码的关键。了解它们的工作原理以及何时使用它们，将为你打开解决报告问题的新思路。

本文的目标是以易于理解的方式逐步解释 SQL 中的窗口函数，让你不必仅仅依赖于死记硬背语法。

我们将涵盖以下内容：

+   关于你应该如何看待窗口函数的解释

+   通过逐渐增加难度的多个例子来进行讲解

+   通过一个具体的真实案例将所学知识付诸实践

+   回顾我们所学的内容

我们的数据集很简单，**包含2023年两地区的六行收入数据。**

![](../Images/0877edc3106b19906797d85e6cf07f76.png)

# 窗口函数是子分组

如果我们对这个数据集运行 `GROUP BY` 并对每个地区的收入进行求和，结果将是显而易见的，对吧？这将只剩下两行数据，每个地区一行，然后是收入的总和：

![](../Images/1dea1a92f4f1781cd737b55d004a47a4.png)

我希望你理解窗口函数的方式非常类似于这个例子，但**与其减少行数，聚合操作将在“后台”运行，值将被添加到现有的行中。**

首先，一个例子：

```py
SELECT
 id,
    date,
    region,
    revenue,
    SUM(revenue) OVER () as total_revenue
FROM
    sales
```

![](../Images/b3b315c4d15c66605f50167b857ebd3d.png)

注意，我们没有使用 `GROUP BY`，数据集保持不变。尽管如此，我们还是能够得到所有收入的总和。在我们更深入地理解它是如何工作的之前，让我们先简要谈谈完整的语法，然后再开始构建我们的知识。

# 窗口函数语法

语法如下：

```py
SUM([some_column]) OVER (PARTITION BY [some_columns] ORDER BY [some_columns])
```

拆解每个部分，得到如下内容：

+   一个聚合或窗口函数：`SUM`、`AVG`、`MAX`、`RANK`、`FIRST_VALUE`

+   `OVER` 关键字，表示这是一个窗口函数

+   `PARTITION BY` 部分，定义了**分组**

+   `ORDER BY` 部分，定义是否为运行函数（我们稍后会讲解）

不要急着理解这些是什么意思，因为当我们通过示例时，这些内容会变得清晰起来。**现在只需要知道，为了定义一个窗口函数，我们将使用** `**OVER**` **关键字。正如我们在第一个示例中看到的那样，这就是唯一的要求。**

# 一步步构建我们的理解

进入一些真正有用的内容，我们现在将在函数中应用一个分组。初始计算会被保留下来，向你展示**我们可以同时运行多个窗口函数**，这意味着我们可以在同一个查询中同时进行不同的聚合，而不需要使用子查询。

```py
SELECT
    id,
    date,
    region,
    revenue,
    SUM(revenue) OVER (PARTITION BY region) as region_total,
    SUM(revenue) OVER () as total_revenue
FROM sales
```

![](../Images/da5c8b809276353b2e95145598e95273.png)

如前所述，**我们使用** `**PARTITION BY**` **来定义我们的分组（窗口），这些分组将被我们的聚合函数使用！** 所以，在保持数据集不变的情况下，我们得到了：

+   每个区域的总收入

+   整个数据集的总收入

我们也不受限于单一分组。类似于 `GROUP BY`，我们可以按区域和季度对数据进行分区，例如：

```py
SELECT
    id,
    date,
    region,
    revenue,
    SUM(revenue) OVER (PARTITION BY 
          region, 
          date_trunc('quarter', date)
    ) AS region_quarterly_revenue
FROM sales
```

![](../Images/8c855d938e778d5a711584e25f15e7f8.png)

**在图像中，我们看到同一区域和季度的唯一两个数据点被归为一组！**

到这里，我希望你已经清楚我们如何将其视为执行一个 `GROUP BY` 操作，但在原地进行，而不减少数据集的行数。当然，我们并不总是希望这样做，但并不罕见会看到有人对数据进行分组，然后再将其与原始数据集合并，这样会使本可以是一个简单窗口函数的查询变得复杂。

继续讲解 `ORDER BY` 关键字。这个关键字定义了一个运行窗口函数。你可能在某一时刻听说过“运行总和”，如果没有，我们应该从一个示例开始，让一切变得清晰。

```py
SELECT
    id,
    date,
    region,
    revenue,
    SUM(revenue) OVER (ORDER BY id) as running_total
FROM sales
```

![](../Images/d1441cd881f2d0e95c2b54bb00aea05f.png)

这里发生的事情是，我们按行逐步累加收入与之前的所有值。这是根据 `id` 列的顺序进行的，但也可以是任何其他列。

这个具体的示例并不是特别有用，因为我们在随机月份和两个区域之间进行求和，但运用我们所学的知识，我们现在可以找到**每个区域的累积收入**。我们通过在每个分组内应用运行总和来实现这一点。

```py
SELECT
    id,
    date,
    region,
    revenue,
    SUM(revenue) OVER (PARTITION BY region ORDER BY date) as running_total
FROM sales
```

![](../Images/444b91883903ba7ecabf49479a7c948c.png)

花些时间确保你理解这里发生了什么：

+   对于每个区域，我们逐月走过并汇总收入。

+   当一个区域完成时，我们就转到下一个区域，从头开始，再次按照月份逐步计算！

有一点很有趣的是，当我们编写这些运行函数时，我们有其他行的“上下文”。我的意思是，要在某一点获得运行和，必须知道前一行的值。这在我们了解到我们可以手动选择聚合前/后多少行时变得更加明显。

```py
SELECT
    id,
    date,
    region,
    revenue,
    SUM(revenue) OVER (ORDER BY id ROWS BETWEEN 1 PRECEDING AND 2 FOLLOWING) 
    AS useless_sum
FROM
    sales
```

![](../Images/5b08b522955119bafaa3e6ece4fcdd07.png)

对于这个查询，我们指定了对于每一行，我们希望查看前一行和后两行的数据，也就是说，我们得到那个范围的和！根据你解决的问题，这可以非常强大，因为它让你完全控制如何对数据进行分组。

最后，在我们进入更复杂的例子之前，我想提到的最后一个函数是`RANK`函数。这是面试中经常被问到的问题，它背后的逻辑和我们到目前为止所学的所有内容是一样的。

```py
SELECT
    *,
    RANK() OVER (PARTITION BY region ORDER BY revenue DESC) as rank,
    RANK() OVER (ORDER BY revenue DESC) as overall_rank
FROM
    sales
ORDER BY region, revenue DESC
```

![](../Images/1a0f2adb51728b3f8640fb02ed75ac6a.png)

就像之前一样，我们使用了`ORDER BY`来指定我们将逐行遍历的顺序，并使用`PARTITION BY`来指定我们的子组。

第一列对每个区域内的每一行进行排名，这意味着在数据集中我们会有多个“排名第一”的值。第二个计算是对数据集中所有行的排名。

# 向前填充缺失数据

这是一个偶尔会出现的问题，要在SQL中解决它需要大量使用窗口函数。为了说明这个概念，我们将使用一个不同的数据集，包含时间戳和温度测量值。**我们的目标是用最后一个测量值填充缺失的温度测量行。**

![](../Images/264bdecb551419927c9cebfe06e1eef4.png)

这是我们期望最终得到的结果：

![](../Images/c1999c3e305edc1c1fbdfadf83226c99.png)

在开始之前，我只是想提一下，如果你使用的是Pandas，你可以通过运行`df.ffill()`轻松解决这个问题，但如果你使用SQL，问题会变得有些棘手。

解决这个问题的第一步是，某种方式地将NULL值与前一个非NULL值分组。可能不清楚我们如何做到这一点，但我希望大家明白这将需要一个**运行函数**。也就是说，这是一个会“逐行走过”的函数，知道何时遇到NULL值，何时遇到非NULL值。

解决方案是使用`COUNT`，更具体地说，是计算温度测量值的数量。在以下查询中，我运行了一个普通的运行计数，并且还对温度值进行了计数。

```py
SELECT
    *,
    COUNT() OVER (ORDER BY timestamp) as normal_count,
    COUNT(temperature) OVER (ORDER BY timestamp) as group_count
from sensor
```

![](../Images/93ed3b3f16f72d91b45c04007f370907.png)

+   在第一个计算中，我们只是简单地对每一行进行递增计数。

+   在第二个查询中，我们统计了所有看到的温度值，而没有计算为NULL的值。

`normal_count`列对我们来说没有用，我只是想展示一下运行中的`COUNT`看起来是什么样子。**然而，我们的第二个计算，**`**group_count**`**让我们更接近解决问题！**

请注意，这种计数方式确保了在NULL值开始之前的第一个值会被计数，然后每当函数遇到NULL时，什么也不会发生。这确保了我们为每个后续的NULL“标记”上我们停止测量时的计数值。

接下来，我们需要将第一个被标记的值复制到同一组中的所有其他行。也就是说，对于组`2`，需要用值`15.0`填充所有数据。

你现在能想到一个我们可以在这里使用的函数吗？这个问题有不止一个答案，但再次强调，我希望至少现在你能明白，我们正在使用一个简单的窗口聚合，并且有`PARTITION BY`。

```py
SELECT
    *,
    FIRST_VALUE(temperature) OVER (PARTITION BY group_count) as filled_v1,
    MAX(temperature) OVER (PARTITION BY group_count) as filled_v2
FROM (
    SELECT
        *,
        COUNT(temperature) OVER (ORDER BY timestamp) as group_count
    from sensor
) as grouped
ORDER BY timestamp ASC
```

![](../Images/188d89d977ccdb549772bfd3700cd8f0.png)

我们可以使用`FIRST_VALUE`或`MAX`来实现我们想要的目标。唯一的目标是得到第一个非NULL值。由于我们知道每个组包含一个非NULL值和一堆NULL值，这两个函数都可以工作！

这个例子是练习窗口函数的一个很好的方式。如果你想要类似的挑战，试着添加两个传感器，然后用该传感器的前一个读数进行向前填充。类似于这样：

![](../Images/abff0d3e5b1c57b048d6bb0e6fed0e84.png)

你能做吗？它没有使用我们到目前为止没有学习过的任何内容。

到目前为止，我们已经了解了关于SQL中窗口函数的一切，所以让我们快速回顾一下！

# 回顾时间

这是我们学到的内容：

+   我们使用`OVER`关键字来编写窗口函数

+   我们使用`PARTITION BY`来指定我们的子组（窗口）

+   如果我们仅提供`OVER()`关键字，我们的窗口就是整个数据集

+   当我们希望进行逐行计算时，我们使用`ORDER BY`，这意味着我们的计算会逐行执行

+   当我们想对数据进行分组以执行聚合操作，但又希望保持数据集原样时，窗口函数非常有用

我希望这能帮助你理解窗口函数是如何工作的，并帮助你将它应用到你需要解决的问题中。

*除非另有说明，否则所有图片均由作者提供*
