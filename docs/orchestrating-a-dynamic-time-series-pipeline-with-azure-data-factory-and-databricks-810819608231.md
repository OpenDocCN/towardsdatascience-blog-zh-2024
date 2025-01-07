# 在Azure中编排动态时间序列管道

> 原文：[https://towardsdatascience.com/orchestrating-a-dynamic-time-series-pipeline-with-azure-data-factory-and-databricks-810819608231?source=collection_archive---------9-----------------------#2024-05-31](https://towardsdatascience.com/orchestrating-a-dynamic-time-series-pipeline-with-azure-data-factory-and-databricks-810819608231?source=collection_archive---------9-----------------------#2024-05-31)

## 探索如何使用Azure Data Factory（ADF）和Databricks构建、触发和参数化一个时间序列数据管道，并附有逐步教程。

[](https://medium.com/@johnleungTJ?source=post_page---byline--810819608231--------------------------------)[![John Leung](../Images/ef45063e759e3450fa7f3c32b2f292c3.png)](https://medium.com/@johnleungTJ?source=post_page---byline--810819608231--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--810819608231--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--810819608231--------------------------------) [John Leung](https://medium.com/@johnleungTJ?source=post_page---byline--810819608231--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--810819608231--------------------------------) ·阅读时间8分钟·2024年5月31日

--

在[上一篇故事](https://medium.com/towards-data-science/feature-engineering-for-time-series-using-pyspark-on-databricks-02b97d62a287)中，我们回顾了PySpark在Databricks上处理时间序列数据的潜力。我鼓励你通过[这里](https://medium.com/towards-data-science/feature-engineering-for-time-series-using-pyspark-on-databricks-02b97d62a287)了解更多内容。在不配置独立Spark实例的情况下，我们可以通过Databricks上的PySpark摄取静态和流数据，执行数据转换，提取有用的时间相关特征，并构建可视化。当处理企业级数据的大规模复杂转换时，PySpark的可扩展性和性能特别具有优势，甚至可以处理PB级别的数据。

所有特征工程任务都成功地在一个Databricks笔记本中完成。然而，这只是构建数据中心系统时数据工程故事的一部分。数据管道的核心部分在于数据编排。

> 数据编排通常指的是对数据流进行集中控制，以便我们可以自动化、管理和监控整个数据管道。

![](../Images/2a39ebad8db26cfd20e2a647df768903.png)

图片由[Julio Rionaldo](https://unsplash.com/@juliorionaldo?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## Azure Data Factory (ADF)与Azure Databricks

为了满足这些需求，行业中最流行的解决方案之一是从[ADF](https://azure.microsoft.com/en-us/products/data-factory#features)平台运行[Azure Databricks](https://azure.microsoft.com/en-gb/products/databricks/#content-card-list-oc803c)笔记本。

ADF 是一个基于云的、无服务器且完全托管的数据集成服务。尽管[Databricks Workflow](https://docs.databricks.com/en/workflows/index.html)提供了一个很好的替代方案，涵盖了部分 ADF 的功能，但选择 ADF 仍然有若干关键优势。例如，ADF 是一个成熟的工具，能够使用连接器与各种数据存储进行集成，包括像 Salesforce 这样的 SaaS 应用和像 Amazon Redshift、Google BigQuery 这样的大数据源。因此，它在数据摄取和集成方面表现良好，尤其是当当前系统与 Databricks 以外的数据系统存在复杂依赖关系时。此外，ADF 简化并便于使用拖放和低代码界面快速构建基本管道。

**在这个实践过程中，我们将深入探讨数据工程项目，并探索 ADF 如何帮助构建一个动态的、骨架型的数据管道，用于时间序列数据。** 我将展示如何在 Azure Databricks 上挂载云存储，通过嵌入的 Notebook 转换数据，并通过 ADF 中的自定义设置动态编排数据。让我们开始吧！

## 初始设置

首先有几个云组件和服务。

**#1 创建一个 Azure 资源组**

这个容器用于保存和分组 Azure 解决方案的资源。我们将把必要的云服务组件放入这个逻辑组中，以便更容易进行构建或部署。

![](../Images/0b3a948d5cc07b62727ff956da16e18d.png)

Azure 资源组（作者提供的图片）

**#2 创建一个 Azure Data Lake Gen 2 存储账户**

你可以根据性能和复制需求选择合适的存储账户。在高级选项卡中，我们启用了分层命名空间以设置[Data Lake Storage Gen 2](https://learn.microsoft.com/en-us/azure/storage/blobs/data-lake-storage-introduction)。这使得既可以存储结构化数据，也可以存储非结构化数据。

![](../Images/1a6a8f5c3ba036f8056555f2f1328079.png)

存储账户（作者提供的图片）

**#3 设置 Azure Databricks 服务**

如果你之前使用过 Databricks，Azure Databricks 服务大体相同。此外，它与其他 Azure 服务原生集成，并提供统一的计费平台。这里有两个[层级](https://azure.microsoft.com/en-us/pricing/details/databricks/)：(1) 标准层——足以满足我们在此的概念验证需求；(2) 高级层——具有标准层的功能，额外提供[Unity Catalog](https://learn.microsoft.com/en-us/azure/databricks/data-governance/unity-catalog/)和可能对于拥有多个 Databricks 工作区的大型企业所需的高级网络功能。

![](../Images/508e7c988c5787278492f29a7cb7c45b.png)

Azure Databricks 工作区（图片来源：作者）

**#4 注册应用程序**

该服务将帮助将 Azure 存储挂载到 Databricks，因此请确保记下应用 ID 和租户 ID，最重要的是应用的密钥值，在你重新访问时是无法查看的。

![](../Images/71347d79943224942a4c8b3849793827.png)

应用注册 — 设置（图片来源：作者）

![](../Images/899152e47dc2eb94104d5fbe94d58e6e.png)

应用注册 — 信息（图片来源：作者）

![](../Images/39029473003e288a43c854dd3933028b.png)

应用注册 — 客户端密钥（图片来源：作者）

然后，授予应用服务对应用服务的访问权限。这是通过将“Storage Blob Data Contributor”角色分配给我们刚注册的应用来实现的。

![](../Images/4307ac970035d3de1b0ff44f080fcd4d.png)

存储账户 — 授予访问权限（1/3）（图片来源：作者）

![](../Images/9e803eab9aa056940edad8db00b91982.png)

存储账户 — 授予访问权限（2/3）（图片来源：作者）

![](../Images/8a7941f7932f3f085535d7aa5b247c1c.png)

存储账户 — 授予访问权限（3/3）（图片来源：作者）

**#5 创建 Azure SQL 数据库**

为了存储转换后的数据框，我们搜索 Azure SQL 资源并选择“单一数据库”作为资源类型。SQL 数据库服务器提供了不同的计算硬件、最大数据大小等选项。你可以在调整服务器配置时即时查看估算的费用摘要。

![](../Images/d1f6640b38d66f1ecf3bc1e31089c0c4.png)

创建 SQL 数据库（1/2）（图片来源：作者）

![](../Images/bf6eaa445f2dbef9ff9a3aef44dd5ef3.png)

创建 SQL 数据库（2/2）（图片来源：作者）

完成所有初始设置后，你就可以探索这些服务是如何相互连接的。

## 准备数据编排管道

**#1 导入数据**

我们首先将电力消耗数据上传到 Azure Data Lake Gen2。这个[数据集](https://www.kaggle.com/datasets/uciml/electric-power-consumption-data-set/data)[许可证为[数据库：开放数据库，内容：数据库内容](https://opendatacommons.org/licenses/dbcl/1-0/)]，来自 Kaggle，采样频率为每分钟一次，数据时间从 2006 年 12 月到 2010 年 11 月。

![](../Images/da8e38c40f958471e0fecc8f8f2c5ea3.png)

上传输入数据（图片来源：作者）

接下来，我们在 Azure Databricks 工作区创建一个 Notebook，并通过定义参数，使用之前存储的 ID 值来挂载存储。

```py
# Define the configuration specifications
configs = {"fs.azure.account.auth.type": "OAuth",
"fs.azure.account.oauth.provider.type": "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider",
"fs.azure.account.oauth2.client.id": "<Client ID>",
"fs.azure.account.oauth2.client.secret": "<Client Secret>",
"fs.azure.account.oauth2.client.endpoint": "https://login.microsoftonline.com/<Tenant ID>/oauth2/token"
}

dbutils.fs.mount(
  source = "abfss://input@adlstsdp.dfs.core.windows.net/", # URI of the object storage
  mount_point = "/mnt/adlstsdp/input",  # local path in the /mnt directory
  extra_configs = configs)
```

为了验证文件访问，我们可以运行以下命令：

```py
dbutils.fs.ls(“/mnt/adlstsdp/input”)

# Output: [FileInfo(path='dbfs:/mnt/adlstsdp/input/household_power_consumption.csv', name='household_power_consumption.csv', size=132960755, modificationTime=1716798010000)]
```

**#2 在 Azure Databricks 中嵌入 Notebook**

本节中的大部分源代码基于我的[上一篇文章](/feature-engineering-for-time-series-using-pyspark-on-databricks-02b97d62a287)。其思路是进行数据清理、转换和特征工程（创建时间相关特征和移动平均特征）。转换后的数据最终写入 Azure 数据库表中。

你可以查看下面的完整代码，了解其实现过程。

```py
# Define file location, file typem and CSV options
file_location = "/mnt/adlstsdp/input/household_power_consumption.csv"
file_type = "csv"
schema = "Date STRING, Time STRING, Global_active_power DOUBLE, Global_reactive_power DOUBLE, Voltage DOUBLE, Global_intensity DOUBLE, Sub_metering_1 DOUBLE, Sub_metering_2 DOUBLE, Sub_metering_3 DOUBLE"
first_row_is_header = "true"
delimiter = ";"

# Read CSV files
org_df = spark.read.format(file_type) \
  .schema(schema) \
  .option("header", first_row_is_header) \
  .option("delimiter", delimiter) \
  .load(file_location)

# Data cleansing and transformation
from pyspark.sql.functions import *
cleaned_df = org_df.na.drop()
cleaned_df = cleaned_df.withColumn("Date", to_date(col("Date"),"d/M/y"))
cleaned_df = cleaned_df.withColumn("Date", cleaned_df["Date"].cast("date"))
cleaned_df = cleaned_df.select(concat_ws(" ", to_date(col("Date"),"d/M/y"), col("Time")).alias("DateTime"), "*")
cleaned_df = cleaned_df.withColumn("DateTime", cleaned_df["DateTime"].cast("timestamp"))
df = cleaned_df.groupby("Date").agg(
    round(sum("Global_active_power"), 2).alias("Total_global_active_power"),
    ).sort(["Date"])

# Add time-related features
df = df.withColumn("year", year("Date"))
df = df.withColumn("month", month("Date"))
df = df.withColumn("week_num", weekofyear("Date"))

# Add lagged value features of total global active power
from pyspark.sql.window import Window
from pyspark.sql.functions import lag

windowSpec = Window.orderBy("Date")
df = df.withColumn("power_lag1", round(lag(col("Total_global_active_power"), 1).over(windowSpec), 2))

# Create delta field
df = df.withColumn("power_lag1_delta", round(col("power_lag1") - col("Total_global_active_power"), 2))

# Create window average fields
def add_window_avg_fields(df, window_sizes):
    for idx, window_size in enumerate(window_sizes, start=1):
        window_col_name = f"avg_power_lag_{idx}"
        windowSpec = Window.orderBy("Date").rowsBetween(-window_size, 0)
        df = df.withColumn(window_col_name, round(avg(col("Total_global_active_power")).over(windowSpec), 2))
    return df

window_sizes = [14, 30]
df = add_window_avg_fields(df, window_sizes)

# Create Exponentially Weighted Moving Average (EWMA) fields
import pyspark.pandas as ps
ps.set_option('compute.ops_on_diff_frames', True)

def add_ewma_fields(df, alphas):
    for idx, alpha in enumerate(alphas, start=1):
        ewma_col_name = f"ewma_power_weight_{idx}"
        windowSpec = Window.orderBy("Date")
        df[ewma_col_name] = df.Total_global_active_power.ewm(alpha=alpha).mean().round(2)
    return df

alphas = [0.2, 0.8]
df_pd = df.pandas_api()
df_pd = add_ewma_fields(df_pd, alphas)
df = df_pd.to_spark()

# Write transformed dataframe to the database table "electric_usage_table"
df.write.format("jdbc") \
    .option("url", "jdbc:sqlserver://sql-db-dp.database.windows.net:1433;databaseName=sql-db-dp") \
    .option("dbtable", "dbo.electric_usage_table") \
    .option("user", "<username>") \
    .option("password", "<password>") \
    .mode("overwrite")  \
    .save()
```

**#3 在 ADF 中构建基本管道**

在ADF中，我们将“Notebook”活动添加到管道环境中，然后配置它以引用Databricks文件夹中的所需Notebook。设置Databricks连接服务，然后在ADF中验证并发布整个活动管道。然后，您可以在“调试”模式下运行管道。

![](../Images/77f80f4bd8dac29b014997ad560cb4d7.png)

管道运行的成功状态（图片由作者提供）

活动状态显示为“已成功”，这意味着数据应该已迁移并插入到Azure SQL数据库表中。我们可以使用查询编辑器查看结果以进行验证。

![](../Images/f9a3cbd5c05477cfd6c63700ff13670f.png)

查询Azure SQL数据库的结果（图片由作者提供）

**#4 自动化管道**

ADF提供的功能远超上述简单实现。例如，我们可以通过创建[基于存储的事件触发器](https://learn.microsoft.com/en-us/azure/data-factory/how-to-create-event-trigger?tabs=data-factory)来自动化管道。确保`Microsoft.EventGrid`已注册为您账户订阅中的资源提供者之一，然后设置触发器：每当新数据集上传到存储帐户时，管道将自动执行。

![](../Images/cd7dbcf9ffdcf90150c136a408b6bf58.png)

在ADF中设置新的触发器（图片由作者提供）

这种类型的触发器在行业中有各种应用场景，例如监控库存水平以补充供应链订单，或追踪客户互动以实现数字营销中的个性化推荐。

**#5 参数化Notebook变量**

为了进一步构建更具动态性的数据信息管道，我们可以使变量更加参数化。例如，在时间序列数据的特征工程中，数据特征的窗口大小最初可能并未优化。窗口大小可能需要根据季节性模式或下游模型微调进行调整。对于这种情况，我们可以通过以下设置进行修改。

![](../Images/3c3140b421b91d991eb48967cfdc610b.png)

设置管道运行的参数（图片由作者提供）

在Notebook中，添加以下代码以创建一个小部件，可以从ADF管道获取参数输入：

```py
# Additional code: Access the current value of the widget
inputWindowSizes = dbutils.widgets.get("inputWindowSizes")
window_sizes = inputWindowSizes.split(",")

# Original function for adding window average features
df = add_window_avg_fields(df, window_sizes)
```

在调整设置和Notebook代码后，我们可以通过提供窗口大小参数值，如30和60，来运行管道。

![](../Images/77ce6fbed57e233a919818d647fdfc8b.png)

为管道运行输入窗口大小值（图片由作者提供）

最后，我们可以通过ADF或Databricks工作区再次监控管道状态。

## 总结

在我们的实践探索中，我们主要使用ADF与Azure Databricks来编排一个动态的时间序列数据管道：

+   设置云资源用于计算、分析和存储。

+   从数据摄取到存储，构建数据管道的骨架。

+   通过创建触发器和参数化变量，为管道带来灵活性。

在企业层面，可能会实施更多的[复杂云架构](https://medium.com/@johnleungTJ/how-to-design-the-ai-architectures-in-azure-for-the-new-era-9531229cfd33)，以满足不断变化的需求，如数据流、模型监控和多模型管道。因此，团队在治理政策和支出管理上的协作变得至关重要，以实现性能、可靠性和成本效益之间的精细平衡。

## 在您离开之前

如果您喜欢这篇文章，我邀请您关注我的[Medium页面](https://medium.com/@johnleungTJ)和[LinkedIn页面](https://www.linkedin.com/in/john-leung-639800115/)。通过这样做，您可以随时了解与数据科学侧项目和机器学习运维（MLOps）演示方法相关的精彩内容。

[](/performing-customer-analytics-with-langchain-and-llms-0af4ea38f7b5?source=post_page-----810819608231--------------------------------) [## 使用LangChain和LLMs进行客户分析

### 发现LangChain在客户分析中的潜力与局限性，并附带实际的实施案例…

towardsdatascience.com](/performing-customer-analytics-with-langchain-and-llms-0af4ea38f7b5?source=post_page-----810819608231--------------------------------) [](/managing-the-technical-debts-of-machine-learning-systems-5b85d420ab9d?source=post_page-----810819608231--------------------------------) [## 管理机器学习系统的技术债务

### 探索通过实施代码持续降低快速交付成本的实践

towardsdatascience.com](/managing-the-technical-debts-of-machine-learning-systems-5b85d420ab9d?source=post_page-----810819608231--------------------------------)
