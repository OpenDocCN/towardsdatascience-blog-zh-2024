# Polars 的实用入门

> 原文：[`towardsdatascience.com/practical-introduction-to-polars-8d9cdca350f1?source=collection_archive---------2-----------------------#2024-09-10`](https://towardsdatascience.com/practical-introduction-to-polars-8d9cdca350f1?source=collection_archive---------2-----------------------#2024-09-10)

## 带有 Pandas 并排示例的实践指南

[](https://medium.com/@npotapov?source=post_page---byline--8d9cdca350f1--------------------------------)![Nikolai Potapov](https://medium.com/@npotapov?source=post_page---byline--8d9cdca350f1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8d9cdca350f1--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8d9cdca350f1--------------------------------) [Nikolai Potapov](https://medium.com/@npotapov?source=post_page---byline--8d9cdca350f1--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8d9cdca350f1--------------------------------) ·14 分钟阅读·2024 年 9 月 10 日

--

![](img/07f4634c04141034295192ff239a64fe.png)

由 Dall-E AI 创建的图像

本文并不是要对比 Polars 和 Pandas 或强调它们的区别。它讲述的是如何通过添加一个新工具来提升数据科学专业人士以及其他数据工作者的工作效率。我喜欢 Polars，因为它是多线程的，开箱即用就提供了强大的性能，并且支持带有查询优化功能的惰性求值。这个工具无疑会提升你的数据技能，并开启新的机会。

尽管 Polars 和 Pandas 是不同的库，但它们在 API 上有相似之处。将它们进行对比可以帮助那些熟悉 Pandas API 的人更容易地开始使用 Polars。即使你不熟悉 Pandas 并且想要开始学习 Polars，这仍然会是一个非常有用且值得的学习过程。

我们将讨论在我的经验中最常用的数据分析操作。为了说明如何使用 Polars，我将考虑一个具有可重现数据的抽象任务，这样你可以在你的电脑上跟随所有步骤进行操作。

假设我们有来自三家在线商店的数据，在这些商店中，我们记录用户行为，如浏览和购买。假设在任何时刻，每家在线商店每种类型的行为只会发生一次，如果发生交易错误，我们的数据可能会缺少产品标识符或其数量。此外，对于我们的任务，我们还需要一个包含每个商品价格的产品目录。

让我们来定义主要任务：计算每个在线商店的总购买金额汇总表。

我将把这个任务分解为以下步骤：

1.  数据准备和 DataFrame 创建。

1.  DataFrame 的总结统计。

1.  检索前五条记录。

1.  重命名列。

1.  更改列类型。

1.  填充缺失值。

1.  移除缺失值。

1.  移除重复记录。

1.  筛选数据。

1.  选择所需的列。

1.  对数据进行分组。

1.  将数据与另一个 DataFrame 合并。

1.  计算新列。

1.  创建数据透视表。

让我们开始吧！

## **数据准备与 DataFrame 创建**

我们有以下数据：

+   `OnlineStore` — 表示商店。

+   `product` — 存储产品 ID。

+   `Action type` — 动作的类型（查看或购买）。

+   `quantity` — 购买或查看的产品数量。

+   `Action_time` — 动作的时间戳。

**需求：**

> *polars==1.6.0
> 
> pandas==2.0.0*

```py
from dataclasses import dataclass
from datetime import datetime, timedelta
from random import choice, gauss, randrange, seed
from typing import Any, Dict

import polars as pl
import pandas as pd

seed(42)
```

```py
base_time= datetime(2024, 8, 31, 0, 0, 0, 0)

user_actions_data = [
    {
        "OnlineStore": choice(["Shop1", "Shop2", "Shop3"]),
        "product": choice(["0001", "0002", "0003"]),
        "quantity": choice([1.0, 2.0, 3.0]),
        "Action type": ("purchase" if gauss() > 0.6 else "view"),
        "Action_time": base_time - timedelta(minutes=randrange(1_000_000)),
    }
    for x in range(1_000_000)
]

corrupted_data = [
    {
        "OnlineStore": choice(["Shop1", "Shop2", "Shop3"]),
        "product": choice(["0001", None]),
        "quantity": choice([1.0, None]),
        "Action type": ("purchase" if gauss() > 0.6 else "view"),
        "Action_time": base_time - timedelta(minutes=randrange(1_000)),
    }
    for x in range(1_000)
]
```

对于产品目录，在我们的案例中，仅包括`product_id`及其价格（`price`）。

```py
product_catalog_data = {"product_id": ["0001", "0002", "0003"], "price": [100, 25, 80]}
```

数据已准备好。现在我们使用 Pandas 和 Polars 创建这些数据的 DataFrame：

```py
# Pandas
user_actions_pd_df = pd.DataFrame(user_actions_data)
corrupted_pd_df = pd.DataFrame(corrupted_data)
product_catalog_pd_df = pd.DataFrame(product_catalog_data)

# Polars
user_actions_pl_df = pl.DataFrame(user_actions_data)
corrupted_pl_df = pl.DataFrame(corrupted_data)
product_catalog_pl_df = pl.DataFrame(product_catalog_data)
```

由于我们有`user_actions_df`和`corrupted_df`，我们将它们连接成一个单一的 DataFrame。

```py
# Pandas
user_actions_pd_df = pd.concat([user_actions_pd_df, corrupted_pd_df])

# Polars
user_actions_pl_df = pl.concat([user_actions_pl_df, corrupted_pl_df])
```

通过这种方式，我们轻松地创建了 DataFrame，以便进一步处理。

> 当然，每种方法都有其自身的参数，因此最好随时查看文档，以避免混淆并正确使用它们。

## DataFrame 的总结统计

加载或准备数据后，快速探索结果数据集是很有用的。对于总结统计信息，方法名称保持不变，但结果可能会有所不同：

```py
# Pandas
user_actions_pd_df.describe(include='all')
```

```py
 OnlineStore  product      quantity Action type          Action_time
count      1001000  1000492  1.000510e+06     1001000              1001000
unique           3        3           NaN           2               632335
top          Shop3     0001           NaN        view  2024-08-30 22:02:00
freq        333931   333963           NaN      726623                    9
first          NaN      NaN           NaN         NaN  2022-10-06 13:23:00
last           NaN      NaN           NaN         NaN  2024-08-30 23:58:00
mean           NaN      NaN  1.998925e+00         NaN                  NaN
std            NaN      NaN  8.164457e-01         NaN                  NaN
min            NaN      NaN  1.000000e+00         NaN                  NaN
25%            NaN      NaN  1.000000e+00         NaN                  NaN
50%            NaN      NaN  2.000000e+00         NaN                  NaN
75%            NaN      NaN  3.000000e+00         NaN                  NaN
max            NaN      NaN  3.000000e+00         NaN                  NaN
```

```py
# Polars
user_actions_pl_df.describe()
```

```py
┌────────────┬─────────────┬─────────┬───────────┬─────────────┬────────────────────────────┐
│ statistic  ┆ OnlineStore ┆ product ┆ quantity  ┆ Action type ┆ Action_time                │
│ ---        ┆ ---         ┆ ---     ┆ ---       ┆ ---         ┆ ---                        │
│ str        ┆ str         ┆ str     ┆ f64       ┆ str         ┆ str                        │
╞════════════╪═════════════╪═════════╪═══════════╪═════════════╪════════════════════════════╡
│ count      ┆ 1001000     ┆ 1000492 ┆ 1.00051e6 ┆ 1001000     ┆ 1001000                    │
│ null_count ┆ 0           ┆ 508     ┆ 490.0     ┆ 0           ┆ 0                          │
│ mean       ┆ null        ┆ null    ┆ 1.998925  ┆ null        ┆ 2023-09-19 03:24:30.981698 │
│ std        ┆ null        ┆ null    ┆ 0.816446  ┆ null        ┆ null                       │
│ min        ┆ Shop1       ┆ 1       ┆ 1.0       ┆ purchase    ┆ 2022-10-06 13:23:00        │
│ 25%        ┆ null        ┆ null    ┆ 1.0       ┆ null        ┆ 2023-03-29 03:09:00        │
│ 50%        ┆ null        ┆ null    ┆ 2.0       ┆ null        ┆ 2023-09-19 06:49:00        │
│ 75%        ┆ null        ┆ null    ┆ 3.0       ┆ null        ┆ 2024-03-11 03:01:00        │
│ max        ┆ Shop3       ┆ 3       ┆ 3.0       ┆ view        ┆ 2024-08-30 23:58:00        │
└────────────┴─────────────┴─────────┴───────────┴─────────────┴────────────────────────────┘
```

正如你所注意到的，Pandas 根据不同的数据类型计算统计信息，并为所有列提供`unique`值。而 Polars 则计算`null_count`值。

此外，在[Polars 文档](https://docs.pola.rs/api/python/dev/reference/dataframe/api/polars.DataFrame.describe.html#polars.DataFrame.describe)中指出：

> 我们不保证`describe`的输出是稳定的。它将显示我们认为有用的统计信息，未来可能会更新。因此，不建议将`describe`用于编程操作（而非交互式探索）。

## **检索前五条记录**

在首次遇到数据时，我们总是想要对其进行探索。除了获取总结统计信息外，查看它包含的实际记录也很重要。为此，我们通常查看前五条记录作为样本。

```py
# Pandas
user_actions_pd_df.head()
```

```py
 OnlineStore product  quantity Action type         Action_time
0       Shop3    0001       1.0        view 2024-05-21 09:24:00
1       Shop3    0001       3.0        view 2023-03-10 15:54:00
2       Shop3    0001       3.0        view 2024-03-24 19:02:00
3       Shop1    0003       3.0        view 2024-08-11 16:16:00
4       Shop3    0001       3.0        view 2024-03-23 11:32:00
```

```py
# Polars
user_actions_pl_df.head()
```

```py
┌─────────────┬─────────┬──────────┬─────────────┬─────────────────────┐
│ OnlineStore ┆ product ┆ quantity ┆ Action type ┆ Action_time         │
│ ---         ┆ ---     ┆ ---      ┆ ---         ┆ ---                 │
│ str         ┆ str     ┆ f64      ┆ str         ┆ datetime[μs]        │
╞═════════════╪═════════╪══════════╪═════════════╪═════════════════════╡
│ Shop3       ┆ 0001    ┆ 1.0      ┆ view        ┆ 2024-05-21 09:24:00 │
│ Shop3       ┆ 0001    ┆ 3.0      ┆ view        ┆ 2023-03-10 15:54:00 │
│ Shop3       ┆ 0001    ┆ 3.0      ┆ view        ┆ 2024-03-24 19:02:00 │
│ Shop1       ┆ 0003    ┆ 3.0      ┆ view        ┆ 2024-08-11 16:16:00 │
│ Shop3       ┆ 0001    ┆ 3.0      ┆ view        ┆ 2024-03-23 11:32:00 │
└─────────────┴─────────┴──────────┴─────────────┴─────────────────────┘
```

Polars 有一个有用的`glimpse()`函数，它提供 DataFrame 的紧凑预览。它不仅返回前 10 条记录（或您通过`max_items_per_column`参数指定的任何数量），还显示数据类型和记录计数。

```py
# Polars
user_actions_pl_df.glimpse()
```

```py
Rows: 1001000
Columns: 5
$ OnlineStore          <str> 'Shop3', 'Shop3', 'Shop3', 'Shop1', 'Shop3', 'Shop2', 'Shop1', 'Shop2', 'Shop1', 'Shop2'
$ product              <str> '0001', '0001', '0001', '0003', '0001', '0003', '0001', '0001', '0002', '0003'
$ quantity             <f64> 1.0, 3.0, 3.0, 3.0, 3.0, 2.0, 3.0, 1.0, 2.0, 1.0
$ Action type          <str> 'view', 'view', 'view', 'view', 'view', 'view', 'view', 'view', 'view', 'view'
$ Action_time <datetime[μs]> 2024-05-21 09:24:00, 2023-03-10 15:54:00, 2024-03-24 19:02:00, 2024-08-11 16:16:00, 2024-03-23 11:32:00, 2023-01-19 14:11:00, 2024-03-27 05:08:00, 2023-11-28 08:18:00, 2023-03-18 15:01:00, 2022-10-29 09:44:00
```

## **重命名列**

在探索数据之后，通常需要对其进行编辑以便进一步使用。如果列名不合适，或者公司有自己的命名规范，您可以轻松地重命名它们。

```py
# Pandas
user_actions_pd_df = user_actions_pd_df.rename(
    columns={
        "OnlineStore": "online_store",
        "product": "product_id",
        "Action type": "action_type",
        "Action_time": "action_dt",
    }
)
```

```py
# user_actions_pd_df.columns
Index(['online_store', 'product_id', 'quantity', 'action_type', 'action_dt'], dtype='object')
```

```py
# Polars
user_actions_pl_df = user_actions_pl_df.rename(
    {
        "OnlineStore": "online_store",
        "product": "product_id",
        "Action type": "action_type",
        "Action_time": "action_dt",
    }
)
```

```py
# user_actions_pl_df.columns
['online_store', 'product_id', 'quantity', 'action_type', 'action_dt']
```

## **更改列类型**

在处理数据时，优化其处理通常是首要任务，而数据类型也不例外。选择正确的类型不仅解锁了可用的函数，还节省了内存。在我们的例子中，我将`quantity`列的类型从`float`更改为`int`。在 Pandas 中，你会使用`astype()`方法，而在 Polars 中，你使用`cast()`方法。

```py
# Pandas
user_actions_pd_df = user_actions_pd_df.astype({"quantity": "Int64"})
```

```py
Int64Index: 1001000 entries, 0 to 999
Data columns (total 5 columns):
 #   Column        Non-Null Count    Dtype         
---  ------        --------------    -----         
 0   online_store  1001000 non-null  object        
 1   product_id    1000492 non-null  object        
 2   quantity      1000510 non-null  Int64         
 3   action_type   1001000 non-null  object        
 4   action_dt     1001000 non-null  datetime64[ns]
dtypes: Int64(1), datetime64ns, object(3)
memory usage: 46.8+ MB
```

```py
# Polars
user_actions_pl_df = user_actions_pl_df.cast({"quantity": pl.Int32})
```

```py
Rows: 1001000
Columns: 5
$ online_store          <str> 
$ product_id            <str> 
$ quantity              <i32> 
$ action_type           <str> 
$ action_dt    <datetime[μs]> 
```

> Polars 有一个特殊的方法`estimated_size()`，用于返回 DataFrame 总（堆）分配大小的估算值。例如：

```py
user_actions_pl_df.estimated_size("mb")
# Result: 24.91054630279541
```

尽管更改类型的方法名称不同，SQL 爱好者会欣赏这一过渡的简便性。

## 填充**缺失值**

在实际项目中，数据很少是完美的，我们经常与经理、分析师及其他系统讨论如何解释数据行为。在数据准备阶段，我特意生成了`corrupted_data`，以便在数据中引入一些混乱。处理缺失值可以轻松成为一本书的主题。

填充缺失值有几种策略，选择哪种方法取决于任务：有时用零填充缺失值就足够了，而有时可能使用均值。在 Polars 中，`fill_null()`方法既可以应用于 DataFrame，也可以应用于特定列。要添加新列或替换现有列中的值，也可以使用`with_columns()`方法。

在我们的例子中，我将用 0 填充`quantity`列中的缺失值：

```py
# Pandas
user_actions_pd_df["quantity"].fillna(0, inplace=True)
```

```py
Data columns (total 5 columns):
 #   Column        Non-Null Count    Dtype         
---  ------        --------------    -----         
 0   online_store  1001000 non-null  object        
 1   product_id    1000492 non-null  object        
 2   quantity      1001000 non-null  Int64         
 3   action_type   1001000 non-null  object        
 4   action_dt     1001000 non-null  datetime64[ns]
dtypes: Int64(1), datetime64ns, object(3)
```

```py
# Polars
user_actions_pl_df = user_actions_pl_df.with_columns(pl.col("quantity").fill_null(0))
```

```py
┌────────────┬──────────────┬────────────┬──────────┬─────────────┬────────────────────────────┐
│ statistic  ┆ online_store ┆ product_id ┆ quantity ┆ action_type ┆ action_dt                  │
│ ---        ┆ ---          ┆ ---        ┆ ---      ┆ ---         ┆ ---                        │
│ str        ┆ str          ┆ str        ┆ f64      ┆ str         ┆ str                        │
╞════════════╪══════════════╪════════════╪══════════╪═════════════╪════════════════════════════╡
│ count      ┆ 1001000      ┆ 1000492    ┆ 1.001e6  ┆ 1001000     ┆ 1001000                    │
│ null_count ┆ 0            ┆ 508        ┆ 0.0      ┆ 0           ┆ 0                          │
└────────────┴──────────────┴────────────┴──────────┴─────────────┴────────────────────────────┘
```

> 在 Polars 中，你可以使用多种策略来填充数据中的缺失值，例如：`{None, 'forward', 'backward', 'min', 'max', 'mean', 'zero', 'one'}`。这些策略的名称不言自明，因此我们不再详细介绍。

同时需要注意，对于浮点型列中的`NaN`值填充，应使用`fill_nan()`方法，这个方法不涉及策略。

## **移除缺失值**

并非所有缺失值都能填充，因此那些无法正确填充并用于进一步计算的值最好删除。在我们的例子中，这适用于`product_id`列，因为没有这个标识符我们无法计算最终结果。

要在 Pandas 和 Polars 中删除含有缺失值的行，可以使用以下方法：

```py
# Pandas
user_actions_pd_df.dropna(subset=["product_id"], inplace=True)
```

```py
Int64Index: 1000492 entries, 0 to 999
Data columns (total 5 columns):
 #   Column        Non-Null Count    Dtype         
---  ------        --------------    -----         
 0   online_store  1000492 non-null  object        
 1   product_id    1000492 non-null  object        
 2   quantity      1000492 non-null  Int64         
 3   action_type   1000492 non-null  object        
 4   action_dt     1000492 non-null  datetime64[ns]
dtypes: Int64(1), datetime64ns, object(3)
```

```py
# Polars
user_actions_pl_df = user_actions_pl_df.drop_nulls(subset=["product_id"])
```

```py
┌────────────┬──────────────┬────────────┬────────────┬─────────────┬────────────────────────────┐
│ statistic  ┆ online_store ┆ product_id ┆ quantity   ┆ action_type ┆ action_dt                  │
│ ---        ┆ ---          ┆ ---        ┆ ---        ┆ ---         ┆ ---                        │
│ str        ┆ str          ┆ str        ┆ f64        ┆ str         ┆ str                        │
╞════════════╪══════════════╪════════════╪════════════╪═════════════╪════════════════════════════╡
│ count      ┆ 1000492      ┆ 1000492    ┆ 1.000492e6 ┆ 1000492     ┆ 1000492                    │
│ null_count ┆ 0            ┆ 0          ┆ 0.0        ┆ 0           ┆ 0                          │
└────────────┴──────────────┴────────────┴────────────┴─────────────┴────────────────────────────┘
```

还需要注意的是，要移除浮点型列中的`NaN`值，应使用`drop_nans()`方法。

## **移除重复记录**

重复记录的最简单情况是当一条记录的所有值与另一条记录完全相同时。在我们的例子中，如果相同的操作在同一时刻在同一家在线商店中被多次记录，就可能会出现重复。我将在发现重复时，只保留最新的值。

在 Pandas 中，去除重复记录使用`drop_duplicates()`方法，而在 Polars 中，使用`unique()`方法。

```py
# Pandas
user_actions_pd_df.drop_duplicates(
    subset=["online_store", "action_type", "action_dt"],
    keep="last",
    inplace=True,
)
```

```py
Data columns (total 5 columns):
 #   Column        Non-Null Count   Dtype         
---  ------        --------------   -----         
 0   online_store  907246 non-null  object        
 1   product_id    907246 non-null  object        
 2   quantity      907246 non-null  Int64         
 3   action_type   907246 non-null  object        
 4   action_dt     907246 non-null  datetime64[ns]
dtypes: Int64(1), datetime64ns, object(3)
```

```py
# Polars
user_actions_pl_df = user_actions_pl_df.unique(
    subset=["online_store", "action_type", "action_dt"],
    keep="last",
)
```

```py
┌────────────┬──────────────┬────────────┬──────────┬─────────────┬────────────────────────────┐
│ statistic  ┆ online_store ┆ product_id ┆ quantity ┆ action_type ┆ action_dt                  │
│ ---        ┆ ---          ┆ ---        ┆ ---      ┆ ---         ┆ ---                        │
│ str        ┆ str          ┆ str        ┆ f64      ┆ str         ┆ str                        │
╞════════════╪══════════════╪════════════╪══════════╪═════════════╪════════════════════════════╡
│ count      ┆ 907246       ┆ 907246     ┆ 907246.0 ┆ 907246      ┆ 907246                     │
│ null_count ┆ 0            ┆ 0          ┆ 0.0      ┆ 0           ┆ 0                          │
└────────────┴──────────────┴────────────┴──────────┴─────────────┴────────────────────────────┘
```

## **过滤数据**

在数据清洗阶段之后，我们需要筛选出与未来计算相关的数据。在 Polars 中，这可以通过一个描述性很强的方法`filter()`来完成。

> 过滤条件不为 True 的行将被丢弃，包括空值。

```py
# Pandas
user_actions_pd_df = user_actions_pd_df.loc[
    user_actions_pd_df["action_type"] == "purchase"
]
```

```py
Int64Index: 262237 entries, 11 to 995
Data columns (total 5 columns):
 #   Column        Non-Null Count   Dtype         
---  ------        --------------   -----         
 0   online_store  262237 non-null  object        
 1   product_id    262237 non-null  object        
 2   quantity      262237 non-null  Int64         
 3   action_type   262237 non-null  object        
 4   action_dt     262237 non-null  datetime64[ns]
dtypes: Int64(1), datetime64ns, object(3)
```

```py
# Polars
user_actions_pl_df = user_actions_pl_df.filter(
    pl.col("action_type") == "purchase"
)
```

```py
┌────────────┬──────────────┬────────────┬──────────┬─────────────┬────────────────────────────┐
│ statistic  ┆ online_store ┆ product_id ┆ quantity ┆ action_type ┆ action_dt                  │
│ ---        ┆ ---          ┆ ---        ┆ ---      ┆ ---         ┆ ---                        │
│ str        ┆ str          ┆ str        ┆ f64      ┆ str         ┆ str                        │
╞════════════╪══════════════╪════════════╪══════════╪═════════════╪════════════════════════════╡
│ count      ┆ 262237       ┆ 262237     ┆ 262237.0 ┆ 262237      ┆ 262237                     │
│ null_count ┆ 0            ┆ 0          ┆ 0.0      ┆ 0           ┆ 0                          │
└────────────┴──────────────┴────────────┴──────────┴─────────────┴────────────────────────────┘
```

## **选择所需的列**

在过滤数据后，您可能需要仅保留对进一步分析相关的列。在 Polars 中，可以使用`select()`方法来实现这一点。

```py
# Pandas
user_actions_pd_df = user_actions_pd_df[
    ["online_store", "action_type", "product_id", "quantity"]
]
```

```py
# Polars
user_actions_pl_df = user_actions_pl_df.select(
    "online_store", "action_type", "product_id", "quantity"
)
```

## **分组数据**

在准备好数据之后，我们可以对其进行聚合，获取每个在线商店和产品的`quantity`总和。我还会保留`action_type`以供后续步骤使用。在 Polars 中，我们使用`group_by()`方法，这与 Pandas 中的`groupby()`方法类似。

```py
# Pandas
user_actions_pd_df = (
    user_actions_pd_df.groupby(["online_store", "product_id", "action_type"])
    .agg({"quantity": "sum"})
    .reset_index()
)
```

```py
 online_store product_id action_type  quantity
0        Shop1       0001    purchase     57772
1        Shop1       0002    purchase     58015
2        Shop1       0003    purchase     58242
3        Shop2       0001    purchase     58256
4        Shop2       0002    purchase     58648
5        Shop2       0003    purchase     58458
6        Shop3       0001    purchase     57891
7        Shop3       0002    purchase     58326
8        Shop3       0003    purchase     59107
```

```py
# Polars
user_actions_pl_df = (
    user_actions_pl_df.group_by(["online_store", "product_id", "action_type"])
    .agg(pl.col("quantity").sum())
)
```

```py
┌──────────────┬────────────┬─────────────┬──────────┐
│ online_store ┆ product_id ┆ action_type ┆ quantity │
│ ---          ┆ ---        ┆ ---         ┆ ---      │
│ str          ┆ str        ┆ str         ┆ i32      │
╞══════════════╪════════════╪═════════════╪══════════╡
│ Shop1        ┆ 0001       ┆ purchase    ┆ 57772    │
│ Shop1        ┆ 0002       ┆ purchase    ┆ 58015    │
│ Shop1        ┆ 0003       ┆ purchase    ┆ 58242    │
│ Shop2        ┆ 0001       ┆ purchase    ┆ 58256    │
│ Shop2        ┆ 0002       ┆ purchase    ┆ 58648    │
│ Shop2        ┆ 0003       ┆ purchase    ┆ 58458    │
│ Shop3        ┆ 0001       ┆ purchase    ┆ 57891    │
│ Shop3        ┆ 0002       ┆ purchase    ┆ 58326    │
│ Shop3        ┆ 0003       ┆ purchase    ┆ 59107    │
└──────────────┴────────────┴─────────────┴──────────┘
```

## **与另一个 DataFrame 进行连接**

要计算总购买量，我们需要将数据与价格目录进行连接。在 Pandas 中，我们有两种方法可以做到这一点，`join()`和`merge()`，它们在具体实现和功能上有所不同。在 Polars 中，我们只使用`join()`方法。

```py
# Pandas
user_actions_pd_df = user_actions_pd_df.merge(product_catalog_pd_df, on='product_id')
```

```py
 online_store product_id action_type  quantity  price
0        Shop1       0001    purchase     57772    100
3        Shop1       0002    purchase     58015     25
6        Shop1       0003    purchase     58242     80
1        Shop2       0001    purchase     58256    100
4        Shop2       0002    purchase     58648     25
7        Shop2       0003    purchase     58458     80
2        Shop3       0001    purchase     57891    100
5        Shop3       0002    purchase     58326     25
8        Shop3       0003    purchase     59107     80
```

```py
# Polars
user_actions_pl_df = user_actions_pl_df.join(product_catalog_pl_df, on='product_id')
```

```py
┌──────────────┬────────────┬─────────────┬──────────┬───────┐
│ online_store ┆ product_id ┆ action_type ┆ quantity ┆ price │
│ ---          ┆ ---        ┆ ---         ┆ ---      ┆ ---   │
│ str          ┆ str        ┆ str         ┆ i32      ┆ i64   │
╞══════════════╪════════════╪═════════════╪══════════╪═══════╡
│ Shop1        ┆ 0001       ┆ purchase    ┆ 57772    ┆ 100   │
│ Shop1        ┆ 0002       ┆ purchase    ┆ 58015    ┆ 25    │
│ Shop1        ┆ 0003       ┆ purchase    ┆ 58242    ┆ 80    │
│ Shop2        ┆ 0001       ┆ purchase    ┆ 58256    ┆ 100   │
│ Shop2        ┆ 0002       ┆ purchase    ┆ 58648    ┆ 25    │
│ Shop2        ┆ 0003       ┆ purchase    ┆ 58458    ┆ 80    │
│ Shop3        ┆ 0001       ┆ purchase    ┆ 57891    ┆ 100   │
│ Shop3        ┆ 0002       ┆ purchase    ┆ 58326    ┆ 25    │
│ Shop3        ┆ 0003       ┆ purchase    ┆ 59107    ┆ 80    │
└──────────────┴────────────┴─────────────┴──────────┴───────┘
```

> 在 Polars 中，`how`参数支持以下值：`{'inner', 'left', 'right', 'full', 'semi', 'anti', 'cross'}`。

## **计算新列**

要计算一个新列或修改现有列，Polars 使用`with_columns()`方法。要为列设置别名，可以使用`alias()`。

```py
# Pandas
user_actions_pd_df["total"] = (
    user_actions_pd_df["price"] * user_actions_pd_df["quantity"]
)
user_actions_pd_df = user_actions_pd_df[
    ["online_store", "action_type", "total"]
]
```

```py
# Polars
user_actions_pl_df = user_actions_pl_df.with_columns(
    (pl.col("price") * pl.col("quantity")).alias("total")
)
user_actions_pl_df = user_actions_pl_df.select(
    "online_store", "action_type", "total"
)
```

另外，您可以直接在`select()`方法中计算一个新列：

```py
# Polars
user_actions_pl_df = user_actions_pl_df.select(
    "online_store",
    "action_type",
    (pl.col("price") * pl.col("quantity")).alias("total"),
)
```

## **创建透视表**

我们的最后一步是创建一个透视表。我们已经计算了每个产品的总销售额，现在我们将轻松计算每个在线商店的总销售额。在 Pandas 中，我们使用`pivot_table()`方法，它允许应用聚合函数。在 Polars 中，我们使用`pivot()`方法来创建透视表。

```py
# Pandas
result_pd = user_actions_pd_df.pivot_table(
    columns="online_store",
    index="action_type",
    values="total",
    aggfunc="sum",
)
```

```py
online_store     Shop1     Shop2     Shop3
action_type                               
purchase      11886935  11968440  11975810
```

```py
# Polars
result_pl = user_actions_pl_df.pivot(
    columns="online_store",
    index="action_type",
    values="total",
    aggregate_function="sum",
)
```

```py
┌─────────────┬──────────┬──────────┬──────────┐
│ action_type ┆ Shop1    ┆ Shop2    ┆ Shop3    │
│ ---         ┆ ---      ┆ ---      ┆ ---      │
│ str         ┆ i64      ┆ i64      ┆ i64      │
╞═════════════╪══════════╪══════════╪══════════╡
│ purchase    ┆ 11886935 ┆ 11968440 ┆ 11975810 │
└─────────────┴──────────┴──────────┴──────────┘
```

> 弃用警告：`pl.DataFrame.pivot`的参数`columns`已弃用，现已重命名为`on`。

现在我们结束我们的旅程。如我们所见，Pandas 和 Polars 的结果一致。到达这里的每个人都很棒且非常努力——你们一定会成功！

## 总结

在本文中，我们通过实际示例和与 Pandas 的比较来探讨 Polars。我演示了如何处理数据准备、描述性统计、缺失值、重复值、过滤、列选择、分组、合并和透视表。通过展示 Pandas 和 Polars 这两者的任务，我突出了使用 Polars 的简便性以及从 Pandas 过渡到 Polars 的容易性。本指南作为高效数据分析中利用 Polars 的实用入门。

## 推荐阅读：

+   [使用 Polars 提高数据转换中的代码质量](https://medium.com/towards-data-science/improving-code-quality-during-data-transformation-with-polars-92997e67c8a9)

## 感谢阅读！

如果您喜欢这篇文章并且想支持我的工作，最好的方式是关注我在[Medium](https://medium.com/@npotapov)。如果你也对像我一样处理数据感兴趣，可以在[LinkedIn](https://www.linkedin.com/in/nikolai-potapov-67269524b/)与我联系。感谢您的掌声——这帮助我知道这篇文章对您有多有用。
