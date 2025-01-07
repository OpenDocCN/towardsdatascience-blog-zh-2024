# 使用 Polars 提高数据转换过程中的代码质量

> 原文：[`towardsdatascience.com/improving-code-quality-during-data-transformation-with-polars-92997e67c8a9?source=collection_archive---------10-----------------------#2024-08-09`](https://towardsdatascience.com/improving-code-quality-during-data-transformation-with-polars-92997e67c8a9?source=collection_archive---------10-----------------------#2024-08-09)

[](https://medium.com/@npotapov?source=post_page---byline--92997e67c8a9--------------------------------)![Nikolai Potapov](https://medium.com/@npotapov?source=post_page---byline--92997e67c8a9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--92997e67c8a9--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--92997e67c8a9--------------------------------) [Nikolai Potapov](https://medium.com/@npotapov?source=post_page---byline--92997e67c8a9--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--92997e67c8a9--------------------------------) ·阅读时长 6 分钟·2024 年 8 月 9 日

--

![](img/32683c335dbe8f17d37fc6ceaac04d46.png)

由 Dall-E AI 生成的图片

在我们作为数据/分析工程师的日常工作中，编写 ETL/ELT 工作流和管道（或者你所在的公司使用其他术语）是我们工作中常规且重要的一部分。然而，在这篇文章中，我将只关注转换阶段。为什么？因为在这一阶段，来自不同来源和不同类型的数据获得了对公司有意义的商业价值。这个阶段非常重要，而且极其微妙，因为一个错误可能会瞬间误导用户，导致他们失去对数据的信任。

为了说明提高代码质量的过程，让我们考虑一个假设的例子。假设有一个网站，我们记录用户的行为，例如他们查看了什么和购买了什么。我们将使用`user_id`表示用户 ID，`product_id`表示产品，`action_type`表示行为类型（可以是查看或购买），`action_dt`表示行为的时间戳。

```py
from dataclasses import dataclass
from datetime import datetime, timedelta
from random import choice, gauss, randrange, seed
from typing import Any, Dict

import polars as pl

seed(42)
```

```py
base_time= datetime(2024, 8, 9, 0, 0, 0, 0)

user_actions_data = [
    {
        "user_id": randrange(10),
        "product_id": choice(["0001", "0002", "0003"]),
        "action_type": ("purchase" if gauss() > 0.6 else "view"),
        "action_dt": base_time - timedelta(minutes=randrange(100_000)),
    }
    for x in range(100_000)
]

user_actions_df = pl.DataFrame(user_actions_data)
```

此外，对于我们的任务，我们还需要一个产品目录，在我们的例子中，它只包含`product_id`和价格（`price`）。我们的数据现在已经准备好用于示例。

```py
product_catalog_data = {"product_id": ["0001", "0002", "0003"], "price": [10, 30, 70]}
product_catalog_df = pl.DataFrame(product_catalog_data)
```

现在，让我们处理第一个任务：创建一个报告，其中包含每个用户在前一天的总购买金额以及购买商品数量与查看商品数量的比例。这个任务并不复杂，可以快速实现。以下是使用 Polars 的实现方式：

```py
yesterday = base_time - timedelta(days=1)
result = (
    user_actions_df.filter(pl.col("action_dt").dt.date() == yesterday.date())
    .join(product_catalog_df, on="product_id")
    .group_by(pl.col("user_id"))
    .agg(
        [
            (
                pl.col("price")
                .filter(pl.col("action_type") == "purchase")
                .sum()
            ).alias("total_purchase_amount"),
            (
                pl.col("product_id").filter(pl.col("action_type") == "purchase").len()
                / pl.col("product_id").filter(pl.col("action_type") == "view").len()
            ).alias("purchase_to_view_ratio"),
        ]
    )
    .sort("user_id")
)
```

这是一种可以部署到生产环境的工作解决方案，有人可能会这么说，但我们不同，因为你已经打开了这篇文章。一开始，我强调过，我将特别关注转换步骤。

如果我们考虑到这段代码的长期维护、测试，并且记住将会有数百个这样的报告，我们必须认识到，每一个后续开发人员对这段代码的理解都将低于前一个开发人员，从而增加每次修改时出现错误的几率。

我想减少这个风险，因此我采用了以下方法：

**步骤 1**：让我们将所有的业务逻辑拆分到一个独立的类中，例如`DailyUserPurchaseReport`。

```py
@dataclass
class DailyUserPurchaseReport:
```

**步骤 2**：让我们定义这个类应该接受的参数：`sources` - 我们工作所需的各种来源，和`params` - 可能会变化的可变参数，在我们的例子中，这可能是报告日期。

```py
@dataclass
class DailyUserPurchaseReport:

    sources: Dict[str, pl.LazyFrame]
    params: Dict[str, Any]
```

**步骤 3**：定义一个方法来执行转换，例如，`execute`。

```py
@dataclass
class DailyUserPurchaseReport:

    sources: Dict[str, pl.LazyFrame]
    params: Dict[str, Any]

    def execute(self) -> pl.DataFrame:
        pass
```

**步骤 4**：将整个过程拆分成独立的函数，每个函数都接受一个`pl.LazyFrame`并返回一个`pl.LazyFrame`。

```py
@dataclass
class DailyUserPurchaseReport:

    sources: Dict[str, pl.LazyFrame]
    params: Dict[str, Any]

    def _filter_actions_by_date(self, frame: pl.LazyFrame) -> pl.LazyFrame:
        pass

    def _enrich_user_actions_from_product_catalog(self, frame: pl.LazyFrame) -> pl.LazyFrame:
        pass

    def _calculate_key_metrics(self, frame: pl.LazyFrame) -> pl.LazyFrame:
        pass

    def execute(self) -> pl.DataFrame:
        pass
```

**步骤 5**：现在，使用魔法函数`pipe`将我们的整个管道连接在一起。这正是我们在各处使用`pl.LazyFrame`的原因：

```py
 def execute(self) -> pl.DataFrame:
        result: pl.DataFrame = (
            self.sources["user_actions"]
            .pipe(self._filter_actions_by_date)
            .pipe(self._enrich_user_actions_from_product_catalog)
            .pipe(self._calculate_key_metrics)
            .collect()
        )
        return result
```

> *建议在管道操作时使用 LazyFrame，以充分利用查询优化和并行化。*

最终代码：

```py
@dataclass
class DailyUserPurchaseReport:
    """
    Generates a report containing the total purchase amount and the ratio of purchased items
    to viewed items from the previous day for each user.

    Attributes:
        sources (Dict[str, pl.LazyFrame]): A dictionary containing the data sources, including:
            - 'user_actions': A LazyFrame containing user actions data.
            - 'product_catalog': A LazyFrame containing product catalog data.
        params (Dict[str, Any]): A dictionary containing parameters, including:
            - 'report_date': The date for which the report should be generated (previous day).
    """

    sources: Dict[str, pl.LazyFrame]
    params: Dict[str, Any]

    def _filter_actions_by_date(self, frame: pl.LazyFrame) -> pl.LazyFrame:
        """
         Filters user actions data to include only records from the specified date.

        Args:
            frame (pl.LazyFrame): A LazyFrame containing user actions data.

        Returns:
            pl.LazyFrame: A LazyFrame containing user actions data filtered by the specified date.
        """
        return frame.filter(pl.col("action_dt").dt.date() == self.params["report_date"])

    def _enrich_user_actions_from_product_catalog(
        self, frame: pl.LazyFrame
    ) -> pl.LazyFrame:
        """
        Joins the user actions data with the product catalog to include product prices.

        Args:
            frame (pl.LazyFrame): A LazyFrame containing user actions data.

        Returns:
            pl.LazyFrame: A LazyFrame containing user actions data enriched with product prices.
        """
        return frame.join(self.sources["product_catalog"], on="product_id")

    def _calculate_key_metrics(self, frame: pl.LazyFrame) -> pl.LazyFrame:
        """
        Calculates the total purchase amount and the ratio of purchased items to viewed items.

        Args:
            frame (pl.LazyFrame): A LazyFrame containing enriched user actions data.

        Returns:
            pl.LazyFrame: A LazyFrame containing the total purchase amount and purchase-to-view ratio for each user.

        """
        return (
            frame.group_by(pl.col("user_id"))
            .agg(
                [
                    (
                        pl.col("price")
                        .filter(pl.col("action_type") == "purchase")
                        .sum()
                    ).alias("total_purchase_amount"),
                    (
                        pl.col("product_id")
                        .filter(pl.col("action_type") == "purchase")
                        .len()
                        / pl.col("product_id").filter(pl.col("action_type") == "view").len()
                    ).alias("purchase_to_view_ratio"),
                ]
            )
            .sort("user_id")
        )

    def execute(self) -> pl.DataFrame:
        """
        Executes the report generation process.

        This method performs the following steps:
            1\. Filters user actions data to include only records from the previous day.
            2\. Joins the filtered user actions data with the product catalog.
            3\. Calculates the total purchase amount and purchase-to-view ratio for each user.
            4\. Returns the final report as a DataFrame.

        Returns:
            pl.DataFrame: A DataFrame containing the total purchase amount and purchase-to-view ratio for each user.
        """
        result: pl.DataFrame = (
            self.sources["user_actions"]
            .pipe(self._filter_actions_by_date)
            .pipe(self._enrich_user_actions_from_product_catalog)
            .pipe(self._calculate_key_metrics)
            .collect()
        )
        return result
```

让我们检查一下执行情况：

```py
# prepare sources
user_actions: pl.LazyFrame = user_actions_df.lazy()
product_catalog: pl.LazyFrame = product_catalog_df.lazy()

# get report date
yesterday: datetime = base_time - timedelta(days=1)

# report calculation
df: pl.DataFrame = DailyUserPurchaseReport(
    sources={"user_actions": user_actions, "product_catalog": product_catalog},
    params={"report_date": yesterday},
).execute()
```

结果：

```py
┌─────────┬───────────────────────┬────────────────────────┐
│ user_id ┆ total_purchase_amount ┆ purchase_to_view_ratio │
│ ---     ┆ ---                   ┆ ---                    │
│ i64     ┆ i64                   ┆ f64                    │
╞═════════╪═══════════════════════╪════════════════════════╡
│ 0       ┆ 1880                  ┆ 0.422018               │
│ 1       ┆ 1040                  ┆ 0.299065               │
│ 2       ┆ 2220                  ┆ 0.541667               │
│ 3       ┆ 1480                  ┆ 0.436782               │
│ 4       ┆ 1240                  ┆ 0.264463               │
│ 5       ┆ 930                   ┆ 0.254717               │
│ 6       ┆ 1080                  ┆ 0.306122               │
│ 7       ┆ 1510                  ┆ 0.345133               │
│ 8       ┆ 2050                  ┆ 0.536842               │
│ 9       ┆ 1320                  ┆ 0.414414               │
└─────────┴───────────────────────┴────────────────────────┘
```

# 奖励

对于使用测试驱动开发（TDD）的人来说，这种方法尤为有益。TDD 强调在实际实现之前编写测试。通过定义清晰的小函数，你可以为每个转换过程编写精确的测试，确保每个函数按预期行为运行。这不仅使过程更加顺畅，还确保了你的转换在每个步骤都经过充分验证。

# 结论

在本文中，我概述了一种使用 Polars 改善数据工作流代码质量的结构化方法。通过将转换步骤隔离并将过程拆分为独立的、可管理的部分，我们确保了我们的代码既稳健又易于维护。通过使用`pl.LazyFrame`和`pipe`函数，我们充分利用了 Polars 在查询优化和并行化方面的能力。这种方法不仅提高了数据转换的效率，还确保了我们处理的数据的完整性和业务相关性。通过遵循这些步骤，你可以创建更可靠、可扩展的数据工作流，最终推动更好的数据驱动决策。

# 分享您的经验

如果你有经验或有用的技巧，欢迎在评论中分享你的意见。了解其他开发者的经验总是很有趣的。
