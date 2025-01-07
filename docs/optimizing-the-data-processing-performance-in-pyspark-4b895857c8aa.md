# 优化PySpark中的数据处理性能

> 原文：[https://towardsdatascience.com/optimizing-the-data-processing-performance-in-pyspark-4b895857c8aa?source=collection_archive---------3-----------------------#2024-11-07](https://towardsdatascience.com/optimizing-the-data-processing-performance-in-pyspark-4b895857c8aa?source=collection_archive---------3-----------------------#2024-11-07)

## PySpark技术与策略，解决常见的性能挑战：一个实用的操作指南

[](https://medium.com/@johnleungTJ?source=post_page---byline--4b895857c8aa--------------------------------)[![John Leung](../Images/ef45063e759e3450fa7f3c32b2f292c3.png)](https://medium.com/@johnleungTJ?source=post_page---byline--4b895857c8aa--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4b895857c8aa--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4b895857c8aa--------------------------------) [John Leung](https://medium.com/@johnleungTJ?source=post_page---byline--4b895857c8aa--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4b895857c8aa--------------------------------) ·阅读时间：9分钟·发布日期：2024年11月7日

--

[Apache Spark](https://spark.apache.org/)由于其强大的分布式数据处理能力，近年来已成为领先的数据分析引擎之一。PySpark是Spark的Python API，常用于个人和企业项目中，以解决数据挑战。例如，我们可以使用PySpark高效地实现[时间序列数据的特征工程](/feature-engineering-for-time-series-using-pyspark-on-databricks-02b97d62a287)，包括数据摄取、提取和可视化。然而，尽管PySpark能够处理大规模数据集，但在一些特定场景下，如极端数据分布和复杂的数据转换流程，性能瓶颈仍然可能出现。

本文将探讨在[Databricks](https://www.databricks.com/)上使用PySpark进行数据处理时常见的性能问题，并介绍各种优化策略，以实现更快的执行速度。

![](../Images/7a51dfd6e0bb834e68f2dbd4ac63cace.png)

图片来源：[Veri Ivanova](https://unsplash.com/@veri_ivanova?utm_source=medium&utm_medium=referral) 来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

假设你开设了一家在线零售店，提供多种产品，主要面向美国客户。你计划通过分析当前交易的购买习惯来满足现有客户的更多需求，并吸引更多新客户。这促使你投入大量精力处理交易记录，作为准备步骤。

## #0 模拟数据

我们首先模拟了 100 万条交易记录（在实际的大数据场景中，预计会处理更大的数据集），这些记录包含了客户 ID、购买的产品和交易细节，如支付方式和总金额。值得一提的是，客户 ID #100 的产品代理商有着庞大的客户群，因此在你的店铺中占据了大部分代发货的购买。

以下是演示此场景的代码：

```py
import csv
import datetime
import numpy as np
import random

# Remove existing ‘retail_transactions.csv’ file, if any
! rm -f /p/a/t/h retail_transactions.csv

# Set the no of transactions and othet configs
no_of_iterations = 1000000
data = []
csvFile = 'retail_transactions.csv'

# Open a file in write mode
with open(csvFile, 'w', newline='') as f:

   fieldnames = ['orderID', 'customerID', 'productID', 'state', 'paymentMthd', 'totalAmt', 'invoiceTime']
   writer = csv.DictWriter(f, fieldnames=fieldnames)
   writer.writeheader()

   for num in range(no_of_iterations):
     # Create a transaction record with random values
     new_txn = {
     'orderID': num,
     'customerID': random.choice([100, random.randint(1, 100000)]),
     'productID': np.random.randint(10000, size=random.randint(1, 5)).tolist(),
     'state': random.choice(['CA', 'TX', 'FL', 'NY', 'PA', 'OTHERS']),
     'paymentMthd': random.choice(['Credit card', 'Debit card', 'Digital wallet', 'Cash on delivery', 'Cryptocurrency']),
     'totalAmt': round(random.random() * 5000, 2),
     'invoiceTime': datetime.datetime.now().isoformat()
     }

     data.append(new_txn)

  writer.writerows(data)
```

在模拟数据之后，我们使用 Databrick 的 Jupyter Notebook 将 CSV 文件加载到 PySpark DataFrame 中。

```py
# Set file location and type
file_location = "/FileStore/tables/retail_transactions.csv"
file_type = "csv"

# Define CSV options
schema = "orderID INTEGER, customerID INTEGER, productID INTEGER, state STRING, paymentMthd STRING, totalAmt DOUBLE, invoiceTime TIMESTAMP"
first_row_is_header = "true"
delimiter = ","

# Read CSV files into DataFrame
df = spark.read.format(file_type) \
  .schema(schema) \
  .option("header", first_row_is_header) \
  .option("delimiter", delimiter) \
  .load(file_location) 
```

我们还创建了一个可重用的装饰器工具，用于衡量和比较每个函数内不同方法的执行时间。

```py
import time

# Measure the excution time of a given function
def time_decorator(func):
  def wrapper(*args, **kwargs):
    begin_time = time.time()
    output = func(*args, **kwargs)
    end_time = time.time()
    print(f"Execution time of function {func.__name__}: {round(end_time - begin_time, 2)} seconds.")
  return output
return wrapper
```

好的，所有准备工作已经完成。接下来我们将探讨以下几个章节中执行性能的不同潜在挑战。

## #1 存储

Spark 使用[弹性分布式数据集（RDD）](https://spark.apache.org/docs/latest/rdd-programming-guide.html)作为其核心构建块，数据默认通常保存在内存中。无论是执行计算（如连接和聚合）还是在集群中存储数据，所有操作都会在统一区域中贡献内存使用。

![](../Images/f7f20c42fa3519fee671c569416280dc.png)

一个包含执行内存和存储内存的统一区域（图源：作者）

如果设计不当，可能导致可用内存不足。这会导致过多的分区溢出到磁盘，从而导致性能下降。

缓存和持久化中间结果或频繁访问的数据集是常见的做法。虽然缓存和持久化具有相同的目的，但它们的存储级别可能有所不同。应当合理利用资源，以确保高效的读写操作。

> 例如，如果转换后的数据会在不同的后续阶段中重复用于计算和算法，建议对这些数据进行缓存。

**代码示例：** 假设我们想要调查使用数字钱包作为支付方式的不同交易记录子集。

+   低效 — 没有缓存

```py
from pyspark.sql.functions import col

@time_decorator
def without_cache(data):
  # 1st filtering
  df2 = data.where(col("paymentMthd") == "Digital wallet")
  count = df2.count()

  # 2nd filtering
  df3 = df2.where(col("totalAmt") > 2000)
  count = df3.count()

  return count

display(without_cache(df))
```

+   高效 — 缓存关键数据集

```py
from pyspark.sql.functions import col

@time_decorator
def after_cache(data):
  # 1st filtering with cache
  df2 = data.where(col("paymentMthd") == "Digital wallet").cache()
  count = df2.count()

  # 2nd filtering
  df3 = df2.where(col("totalAmt") > 2000)
  count = df3.count()

  return count

display(after_cache(df))
```

缓存之后，即使我们想要根据不同的交易金额阈值或其他数据维度来过滤转换后的数据集，执行时间也会更易于控制。

## #2 洗牌

当我们执行如连接 DataFrame 或按数据字段分组的操作时，会发生洗牌。这是必要的，目的是将所有记录重新分布到集群中，并确保具有相同键的记录位于同一个节点。这有助于同时处理并合并结果。

![](../Images/ab84ce7db5a3682b102879ebcf4c8646.png)

洗牌连接（图源：作者）

然而，这种洗牌操作是代价高昂的——由于数据在节点间的移动，执行时间长且额外的网络开销。

为了减少洗牌操作，有几种策略：

(1) 对于小数据集，使用广播变量，将只读副本发送到每个工作节点进行本地处理

> 虽然“较小”数据集通常定义为每个执行器最大内存阈值为8GB，但广播的理想大小应通过针对特定案例的实验来确定。

![](../Images/23bfddffdcd8abbc41eace540e8e045e.png)

广播连接（作者图片）

(2) 提前过滤，尽早尽可能减少处理的数据量；

(3) 控制分区数量，以确保最佳性能

**代码示例：** 假设我们想返回与我们的州列表匹配的交易记录及其全名

+   低效——大数据集与小数据集之间的shuffle连接

```py
from pyspark.sql.functions import col

@time_decorator
def no_broadcast_var(data):
  # Create small dataframe
  small_data = [("CA", "California"), ("TX", "Texas"), ("FL", "Florida")]
  small_df = spark.createDataFrame(small_data, ["state", "stateLF"])

  # Perform joining
  result_no_broadcast = data.join(small_df, "state")

  return result_no_broadcast.count()

display(no_broadcast_var(df))
```

+   高效——使用广播变量将大数据集与小数据集合并

```py
from pyspark.sql.functions import col, broadcast

@time_decorator
def have_broadcast_var(data):
  small_data = [("CA", "California"), ("TX", "Texas"), ("FL", "Florida")]
  small_df = spark.createDataFrame(small_data, ["state", "stateFullName"])

  # Create broadcast variable and perform joining
  result_have_broadcast = data.join(broadcast(small_df), "state")

  return result_have_broadcast.count()

display(have_broadcast_var(df))
```

## #3 倾斜性

数据有时会分布不均，尤其是用于处理的键字段。这会导致分区大小不平衡，其中某些分区比平均值大或小得多。

> 由于执行性能受到最长运行任务的限制，因此需要解决过载节点的问题。

一种常见的方法是加盐。其原理是通过向倾斜键添加随机数，使得数据在分区中更加均匀分布。假设在基于倾斜键进行聚合时，我们将使用加盐后的键进行聚合，然后再使用原始键进行聚合。另一种方法是重新分区，它通过增加分区的数量来帮助数据更均匀地分布。

![](../Images/119db606882ab6d12a63942cc007751e.png)

数据分布——加盐前后的情况（作者图片）

**代码示例：** 我们想聚合一个不对称的数据集，主要由客户ID #100引起的倾斜。

+   低效——直接使用倾斜键

```py
from pyspark.sql.functions import col, desc

@time_decorator
def no_salting(data):
  # Perform aggregation
  agg_data = data.groupBy("customerID").agg({"totalAmt": "sum"}).sort(desc("sum(totalAmt)"))
  return agg_data

display(no_salting(df))
```

+   高效——使用加盐的倾斜键进行聚合

```py
from pyspark.sql.functions import col, lit, concat, rand, split, desc

@time_decorator
def have_salting(data):
  # Salt the customerID by adding the suffix
  salted_data = data.withColumn("salt", (rand() * 8).cast("int")) \
                .withColumn("saltedCustomerID", concat(col("customerID"), lit("_"), col("salt")))

  # Perform aggregation
 agg_data = salted_data.groupBy("saltedCustomerID").agg({"totalAmt": "sum"})

 # Remove salt for further aggregation
 final_result = agg_data.withColumn("customerID", split(col("saltedCustomerID"), "_")[0]).groupBy("customerID").agg({"sum(totalAmt)": "sum"}).sort(desc("sum(sum(totalAmt))"))

 return final_result

display(have_salting(df))
```

向倾斜键添加一个随机的前缀或后缀都可以有效。通常，5到10个随机值是一个很好的起点，可以在扩展数据和保持高复杂性之间取得平衡。

## #4 序列化

人们通常更倾向于使用[用户定义函数（UDFs）](https://spark.apache.org/docs/3.5.2/sql-ref-functions-udf-scalar.html)，因为它在定制数据处理逻辑方面更灵活。然而，UDFs是按行逐一操作的。代码需要被Python解释器序列化，发送到执行器JVM，然后再反序列化。这会产生高昂的序列化开销，且阻碍Spark对代码的优化和高效处理。

> 简单直接的方法是尽可能避免使用UDFs。

我们应首先考虑使用[内置 Spark 函数](https://spark.apache.org/docs/latest/api/sql/)，这些函数可以处理聚合、数组/映射操作、日期/时间戳以及 JSON 数据处理等任务。如果内置函数无法满足你的需求，确实可以考虑使用[pandas *UDFs*](https://databricks.com/blog/2017/10/30/introducing-vectorized-udfs-for-pyspark.html)。与 UDFs 相比，它们建立在 Apache Arrow 基础上，具有更低的开销和更高的性能。

**代码示例：** 交易价格根据来源州进行折扣。

+   低效 — 使用 UDF

```py
from pyspark.sql.functions import udf
from pyspark.sql.types import DoubleType
from pyspark.sql import functions as F
import numpy as np

# UDF to calculate discounted amount
def calculate_discount(state, amount):
  if state == "CA":
    return amount * 0.90  # 10% off
  else:
    return amount * 0.85  # 15% off

discount_udf = udf(calculate_discount, DoubleType())

@time_decorator
def have_udf(data):
  # Use the UDF
  discounted_data = data.withColumn("discountedTotalAmt", discount_udf("state", "totalAmt"))

  # Show the results
  return discounted_data.select("customerID", "totalAmt", "state", "discountedTotalAmt").show()

display(have_udf(df))
```

+   高效 — 使用内置的 PySpark 函数

```py
from pyspark.sql.functions import when

@time_decorator
def no_udf(data):
  # Use when and otherwise to discount the amount based on conditions 
  discounted_data = data.withColumn(
  "discountedTotalAmt",
  when(data.state == "CA", data.totalAmt * 0.90)  # 10% off
  .otherwise(data.totalAmt * 0.85))  # 15% off

  # Show the results
  return discounted_data.select("customerID", "totalAmt", "state", "discountedTotalAmt").show()

display(no_udf(df))
```

在这个示例中，我们使用内置的 PySpark 函数“when”和“otherwise”来有效地按顺序检查多个条件。基于我们对这些函数的熟悉，示例几乎是无限的。例如，`pyspark.sql.functions.transform`，一个帮助对输入数组中的每个元素应用转换的函数，自 PySpark 3.1.0 版本开始引入。

## #5 溢出

如在存储部分讨论的那样，溢出是由于内存不足以容纳所有所需数据，导致将临时数据从内存写入磁盘。我们提到的许多性能问题都与溢出有关。例如，在分区之间洗牌大量数据的操作，容易导致内存耗尽并随之发生溢出。

![](../Images/8e0391d49e71ebd4918378b38ed64fe2.png)

由于内存不足引起的溢出不同场景（图像由作者提供）

审查 Spark UI 中的性能指标至关重要。如果我们发现溢出（内存）和溢出（磁盘）的统计数据，那么溢出可能是长时间运行任务的原因。为了解决这个问题，可以尝试实例化一个每个工作节点有更多内存的集群，例如通过调节配置值`spark.executor.memory`来增加执行进程的内存大小；另外，我们还可以配置`spark.memory.fraction`来调整分配给执行和存储的内存量。

## 总结

我们遇到了一些常见的导致 PySpark 性能下降的因素，以及可能的改进方法：

+   存储：使用缓存和持久化存储常用的中间结果

+   Shuffle：为小数据集使用广播变量，以促进 Spark 的本地处理

+   偏斜：执行加盐或重新分区以更均匀地分布偏斜数据

+   序列化：更倾向于使用内置 Spark 函数以优化性能

+   溢出：调整配置值以明智地分配内存

最近，[自适应查询执行（AQE）](https://docs.databricks.com/en/optimizations/aqe.html)被提出用于基于运行时统计信息对查询进行动态规划和重新规划。这支持查询执行过程中发生的不同查询重新优化特性，从而成为一种出色的优化技术。然而，在初期设计阶段理解数据特征仍然至关重要，因为这有助于制定更好的策略，以编写有效的代码和查询，并利用 AQE 进行微调。

## 在你离开之前

如果你喜欢这篇文章，欢迎关注我的[Medium 页面](https://medium.com/@johnleungTJ)和[LinkedIn 页面](https://www.linkedin.com/in/john-leung-639800115/)。通过这样做，你可以及时获取有关数据科学副项目、机器学习运维（MLOps）示范以及项目管理方法学的精彩内容。

[](/simplifying-the-python-code-for-data-engineering-projects-95f0c41dc58a?source=post_page-----4b895857c8aa--------------------------------) [## 简化数据工程项目中的 Python 代码

### 用于数据摄取、验证、处理和测试的 Python 技巧与技术：实用的操作流程

towardsdatascience.com](/simplifying-the-python-code-for-data-engineering-projects-95f0c41dc58a?source=post_page-----4b895857c8aa--------------------------------) [](/feature-engineering-for-time-series-using-pyspark-on-databricks-02b97d62a287?source=post_page-----4b895857c8aa--------------------------------) [## 使用 PySpark 在 Databricks 上进行时间序列特征工程

### 探索 PySpark 在时间序列数据中的潜力：摄取、提取和可视化数据，并附带实践…

towardsdatascience.com](/feature-engineering-for-time-series-using-pyspark-on-databricks-02b97d62a287?source=post_page-----4b895857c8aa--------------------------------)
