# 自助式机器学习与关系型深度学习

> 原文：[https://towardsdatascience.com/self-service-ml-with-relational-deep-learning-beb693a21d5b?source=collection_archive---------9-----------------------#2024-10-22](https://towardsdatascience.com/self-service-ml-with-relational-deep-learning-beb693a21d5b?source=collection_archive---------9-----------------------#2024-10-22)

## 直接在关系型数据库上进行机器学习

[](https://medium.com/@brechterlaurin?source=post_page---byline--beb693a21d5b--------------------------------)[![Laurin Brechter](../Images/5a68b96bddf86846a2bef9d482ef9dd3.png)](https://medium.com/@brechterlaurin?source=post_page---byline--beb693a21d5b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--beb693a21d5b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--beb693a21d5b--------------------------------) [Laurin Brechter](https://medium.com/@brechterlaurin?source=post_page---byline--beb693a21d5b--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--beb693a21d5b--------------------------------) ·阅读时间7分钟·2024年10月22日

--

![](../Images/fd3ee51120626ff5eccf5c3fc113c1db.png)

我们数据集的关系模式，[来源](https://www.kaggle.com/datasets/mmohaiminulislam/ecommerce-data-analysis/data)；图像由作者提供

在这篇博客中，我们将深入探讨一种有趣的新型深度学习（DL）方法——关系型深度学习（RDL）。我们还将通过在一个实际的电子商务公司数据库（而不是数据集！）上进行RDL，来获得一些实践经验。

# 简介

在现实世界中，我们通常会有一个关系型数据库，我们希望在其上执行一些机器学习任务。但是，特别是当数据库高度规范化时，这意味着需要大量耗时的特征工程，而且由于需要进行许多聚合操作，细节会丢失。此外，我们可以构造出许多不同的特征组合，而每种组合可能都会带来良好的性能[2]。这意味着我们很可能会遗漏一些对机器学习任务相关的信息。

这类似于计算机视觉的早期阶段，在深度神经网络出现之前，当时的特征是通过像素值手工构建的。如今，模型直接处理原始像素，而不再依赖于这一中间层。

# 关系型深度学习

关系型深度学习（RDL）承诺为表格学习带来相同的效果。也就是说，它通过直接在关系型数据库上进行学习，去除了构建特征矩阵的额外步骤。它通过将数据库及其关系转化为图形来实现这一点，其中表格中的每一行成为一个节点，表格之间的关系成为边缘。行的值存储在节点内部，作为节点特征。

*在这篇博客文章中，我们将使用来自Kaggle的这个* [*电子商务数据集*](https://www.kaggle.com/datasets/mmohaiminulislam/ecommerce-data-analysis/data) *，它包含了一个电子商务平台的交易数据，采用星型模式，中央事实表（交易）和一些维度表。完整的代码可以在这个* [*笔记本*](https://github.com/LaurinBrechter/GraphTheory/blob/main/rdl/rdl_ecommerce.ipynb)*中找到。*

在整篇博客文章中，我们将使用 [relbench](https://github.com/snap-stanford/relbench) 库来执行 [RDL](https://relbench.stanford.edu/)。在relbench中，我们必须做的第一件事是指定我们关系数据库的模式。下面是我们如何为数据库中的“transactions”表指定模式的一个示例。我们将表格作为pandas数据框传递，并指定主键和时间戳列。主键列用于唯一标识实体。时间戳确保当我们想要预测未来的交易时，我们只能从过去的交易中学习。在图中，这意味着信息只能从时间戳较低（即过去）的节点流向时间戳较高的节点。此外，我们还指定了关系中存在的外键。在本例中，“transactions”表有一列“customer_key”，它是一个指向“customer_dim”表的外键。

```py
tables['transactions'] = Table(
            df=pd.DataFrame(t),
            pkey_col='t_id',
            fkey_col_to_pkey_table={
                'customer_key': 'customers',
                'item_key': 'products',
                'store_key': 'stores'
            },
            time_col='date'
        )
```

其余的表格需要以相同的方式进行定义。请注意，如果你已经有了数据库模式，这个过程也可以自动化。由于数据集来自Kaggle，我需要手动创建模式。我们还需要将日期列转换为实际的pandas日期时间对象，并去除任何NaN值。

```py
class EcommerceDataBase(Dataset):
    # example of creating your own dataset: https://github.com/snap-stanford/relbench/blob/main/tutorials/custom_dataset.ipynb

    val_timestamp = pd.Timestamp(year=2018, month=1, day=1)
    test_timestamp = pd.Timestamp(year=2020, month=1, day=1)

    def make_db(self) -> Database:

        tables = {}

        customers = load_csv_to_db(BASE_DIR + '/customer_dim.csv').drop(columns=['contact_no', 'nid']).rename(columns={'coustomer_key': 'customer_key'})
        stores = load_csv_to_db(BASE_DIR + '/store_dim.csv').drop(columns=['upazila'])
        products = load_csv_to_db(BASE_DIR + '/item_dim.csv')
        transactions = load_csv_to_db(BASE_DIR + '/fact_table.csv').rename(columns={'coustomer_key': 'customer_key'})
        times = load_csv_to_db(BASE_DIR + '/time_dim.csv')

        t = transactions.merge(times[['time_key', 'date']], on='time_key').drop(columns=['payment_key', 'time_key', 'unit'])
        t['date'] = pd.to_datetime(t.date)
        t = t.reset_index().rename(columns={'index': 't_id'})
        t['quantity'] = t.quantity.astype(int)
        t['unit_price'] = t.unit_price.astype(float)
        products['unit_price'] = products.unit_price.astype(float)
        t['total_price'] = t.total_price.astype(float)

        print(t.isna().sum(axis=0))
        print(products.isna().sum(axis=0))
        print(stores.isna().sum(axis=0))
        print(customers.isna().sum(axis=0))

        tables['products'] = Table(
            df=pd.DataFrame(products),
            pkey_col='item_key',
            fkey_col_to_pkey_table={},
            time_col=None
        )

        tables['customers'] = Table(
            df=pd.DataFrame(customers),
            pkey_col='customer_key',
            fkey_col_to_pkey_table={},
            time_col=None
        )

        tables['transactions'] = Table(
            df=pd.DataFrame(t),
            pkey_col='t_id',
            fkey_col_to_pkey_table={
                'customer_key': 'customers',
                'item_key': 'products',
                'store_key': 'stores'
            },
            time_col='date'
        )

        tables['stores'] = Table(
            df=pd.DataFrame(stores),
            pkey_col='store_key',
            fkey_col_to_pkey_table={}
        )

        return Database(tables)
```

至关重要的是，作者引入了训练表的概念。这个训练表本质上定义了机器学习任务。这里的思路是，我们希望预测数据库中某个实体的未来状态（即未来的值）。我们通过指定一个表来实现这一点，该表中的每一行都有一个时间戳、实体的标识符和我们希望预测的某些值。id 用于指定实体，时间戳则指定我们需要预测该实体的时间点。这也将限制可用于推断该实体值的数据（即仅限过去的数据）。值本身就是我们想要预测的内容（即真实值）。

在我们的案例中，我们有一个在线平台和顾客。我们想要预测顾客在接下来的30天内的收入。我们可以通过执行DuckDB的SQL语句来创建训练表。这就是RDL的巨大优势，因为我们可以仅通过SQL创建任何类型的机器学习任务。例如，我们可以定义一个查询来选择买家在接下来的30天内的购买次数，从而做出客户流失预测。

```py
df = duckdb.sql(f"""
            select
                timestamp,
                customer_key,
                sum(total_price) as revenue
            from
                timestamp_df t
            left join
                transactions ta
            on
                ta.date <= t.timestamp + INTERVAL '{self.timedelta}'
                and ta.date > t.timestamp
            group by timestamp, customer_key
        """).df().dropna()
```

结果将是一个表格，其中卖家ID作为我们要预测的实体的键，收入作为目标，时间戳作为我们需要进行预测的时间（即我们只能使用直到这个时间点的数据来进行预测）。

![](../Images/249add07383580218f2c4e9e3b3cf33d.png)

训练表格；图片来源：作者

以下是创建“customer_revenue”任务的完整代码。

```py
class CustomerRevenueTask(EntityTask):
    # example of custom task: https://github.com/snap-stanford/relbench/blob/main/tutorials/custom_task.ipynb

    task_type = TaskType.REGRESSION
    entity_col = "customer_key"
    entity_table = "customers"
    time_col = "timestamp"
    target_col = "revenue"
    timedelta = pd.Timedelta(days=30) # how far we want to predict revenue into the future.
    metrics = [r2, mae]
    num_eval_timestamps = 40

    def make_table(self, db: Database, timestamps: "pd.Series[pd.Timestamp]") -> Table:

        timestamp_df = pd.DataFrame({"timestamp": timestamps})

        transactions = db.table_dict["transactions"].df

        df = duckdb.sql(f"""
            select
                timestamp,
                customer_key,
                sum(total_price) as revenue
            from
                timestamp_df t
            left join
                transactions ta
            on
                ta.date <= t.timestamp + INTERVAL '{self.timedelta}'
                and ta.date > t.timestamp
            group by timestamp, customer_key
        """).df().dropna()

        print(df)

        return Table(
            df=df,
            fkey_col_to_pkey_table={self.entity_col: self.entity_table},
            pkey_col=None,
            time_col=self.time_col,
        )
```

有了这些，我们已经完成了大部分工作。剩下的工作流程将是类似的，独立于ML任务。我能够从[示例笔记本](https://github.com/snap-stanford/relbench/blob/main/tutorials/train_model.ipynb)中复制大部分代码，这些代码是relbench提供的。

例如，我们需要对节点特征进行编码。在这里，我们可以使用GloVe嵌入来编码所有文本特征，如产品描述和产品名称。

```py
from typing import List, Optional
from sentence_transformers import SentenceTransformer
from torch import Tensor

class GloveTextEmbedding:
    def __init__(self, device: Optional[torch.device
                                       ] = None):
        self.model = SentenceTransformer(
            "sentence-transformers/average_word_embeddings_glove.6B.300d",
            device=device,
        )

    def __call__(self, sentences: List[str]) -> Tensor:
        return torch.from_numpy(self.model.encode(sentences))
```

之后，我们可以将这些转换应用于我们的数据，并构建出图。

```py
from torch_frame.config.text_embedder import TextEmbedderConfig
from relbench.modeling.graph import make_pkey_fkey_graph

text_embedder_cfg = TextEmbedderConfig(
    text_embedder=GloveTextEmbedding(device=device), batch_size=256
)

data, col_stats_dict = make_pkey_fkey_graph(
    db,
    col_to_stype_dict=col_to_stype_dict,  # speficied column types
    text_embedder_cfg=text_embedder_cfg,  # our chosen text encoder
    cache_dir=os.path.join(
        root_dir, f"rel-ecomm_materialized_cache"
    ),  # store materialized graph for convenience
)
```

剩下的代码将是从标准层构建GNN、编写训练循环以及进行一些评估。我会将这些代码省略在这篇博客中，因为它非常标准且在任务之间相同。你可以在[这里](https://github.com/LaurinBrechter/GraphTheory/tree/main/rdl)查看该笔记本。

![](../Images/885dfaed5f9ea9705ffb86ecb8d1fca9.png)

训练结果，图片来源：作者

因此，我们可以训练这个GNN，使其达到大约0.3的r2和500的MAE。这意味着它预测卖家未来30天的收入时，平均误差为+-$500。当然，我们无法知道这是否算好，可能通过结合经典ML和特征工程，我们能够获得80%的r2。

# 结论

关系深度学习是一种有趣的新型ML方法，特别是在我们拥有复杂的关系模式时，人工特征工程会显得过于繁琐。它使我们能够仅通过SQL定义ML任务，这对于那些没有深入数据科学背景但懂一些SQL的人尤其有用。这也意味着我们可以快速迭代并进行大量不同任务的实验。

与此同时，这种方法也存在一些问题，例如训练GNN的难度和从关系模式构建图的复杂性。此外，问题在于RDL在性能上能否与经典的ML模型竞争。过去，我们已经看到像XGBoost这样的模型在表格预测问题上比神经网络更优。

# 参考文献

+   [1] Robinson, Joshua, 等. “RelBench: A Benchmark for Deep Learning on Relational Databases.” *arXiv*, 2024, [https://arxiv.org/abs/2407.20060](https://arxiv.org/abs/2407.20060)。

+   [2] Fey, Matthias, 等. “Relational deep learning: Graph representation learning on relational databases.” *arXiv预印本 arXiv:2312.04615* (2023)。

+   [3] Schlichtkrull, Michael, 等. “使用图卷积网络对关系数据建模。” *语义网：第十五届国际会议，ESWC 2018，希腊克里特岛赫拉克利翁，2018年6月3日至7日，会议录 15*. 施普林格国际出版社，2018年。
