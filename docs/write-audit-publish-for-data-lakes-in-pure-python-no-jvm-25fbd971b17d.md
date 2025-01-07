# 用纯Python（无JVM）实现的数据湖写入-审计-发布

> 原文：[https://towardsdatascience.com/write-audit-publish-for-data-lakes-in-pure-python-no-jvm-25fbd971b17d?source=collection_archive---------4-----------------------#2024-04-12](https://towardsdatascience.com/write-audit-publish-for-data-lakes-in-pure-python-no-jvm-25fbd971b17d?source=collection_archive---------4-----------------------#2024-04-12)

## 使用Apache Iceberg、Lambdas和Project Nessie的开源WAP实现，全部通过Python运行

[](https://medium.com/@ciro.greco?source=post_page---byline--25fbd971b17d--------------------------------)[![Ciro Greco](../Images/4a20e5d435998e8d8ff7aeac1f8ff60d.png)](https://medium.com/@ciro.greco?source=post_page---byline--25fbd971b17d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--25fbd971b17d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--25fbd971b17d--------------------------------) [Ciro Greco](https://medium.com/@ciro.greco?source=post_page---byline--25fbd971b17d--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--25fbd971b17d--------------------------------) ·阅读时间9分钟·2024年4月12日

--

![](../Images/3f6c7fd882cb9f69244db1e5fe0bc7b6.png)

看，妈妈：没有JVM！图片来自[Zac Ong](https://unsplash.com/@zacong?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 引言

在这篇博客文章中，我们提供了一个简明的参考实现，用于在数据湖上实施写入-审计-发布（WAP）模式，使用[Apache Iceberg](https://iceberg.apache.org/)作为开放表格格式，并使用[Project Nessie](https://projectnessie.org/)作为支持类似git语义的数据目录。

我们选择[Nessie](https://projectnessie.org/)是因为它的分支功能提供了一个良好的抽象，能够实现WAP设计。最重要的是，我们选择在[PyIceberg](https://py.iceberg.apache.org/)上构建，以消除开发者体验中对JVM的需求。事实上，要运行整个项目，包括集成的应用程序，我们只需要Python和AWS。

虽然[Nessie](https://projectnessie.org/)在技术上是用Java构建的，但在本项目中，数据目录通过[AWS Lightsail](https://aws.amazon.com/lightsail/)作为容器运行，我们将仅通过其端点与之交互。因此，我们可以仅使用Python表达整个WAP逻辑，包括下游查询！

由于[PyIceberg](https://py.iceberg.apache.org/)相对较新，实际上很多功能并不直接支持。特别是，写入功能仍处于初期阶段，Iceberg表的分支仍不被支持。因此，您在这里看到的内容是我们自己进行的一些原创工作，以便直接从Python中使[Nessie](https://projectnessie.org/)支持Iceberg表的分支。

所以，这一切或多或少地发生了。

# WAP到底是什么？

2017年，Netflix的Michelle Winters在[talked](https://www.youtube.com/watch?v=fXHdeBnpXrg)中谈到了一个名为“写入-审计-发布”（Write-Audit-Publish，WAP）的数据设计模式。WAP本质上是一种功能性设计，旨在使数据质量检查在数据提供给下游消费者之前**更容易实现**。

例如，一个非典型的用例是数据摄取时的数据质量。流程看起来像是创建一个临时环境，并对新摄取的数据进行质量测试，然后再将这些数据提供给任何下游应用程序。

正如名称所示，基本上有三个阶段：

1.  ***写入。*** 将数据放在一个下游消费者无法访问的位置（例如，暂存环境或分支）。

1.  ***审计。*** 转换和测试数据，确保它符合规格（例如，检查模式是否突然变化，或是否存在意外值，如NULL）。

1.  ***发布。*** 将数据放在消费者可以读取的地方（例如，生产数据湖）。

![](../Images/fed5dd1e2f2e05ee22ab00dc9438b184.png)

图片来自作者

这只是WAP模式应用的一种可能示例。很容易看出，它如何应用于数据生命周期的不同阶段，从ETL和数据摄取，到支持分析和机器学习应用的复杂数据管道。

尽管非常有用，[WAP仍然不太普及](https://lakefs.io/blog/data-engineering-patterns-write-audit-publish/)，而且直到最近，企业才开始更系统地思考它。开放表格格式和像[Nessie](https://projectnessie.org/)和[LakeFS](https://lakefs.io/)这样的项目的兴起正在加速这一过程，但它仍然有些*前卫*。

无论如何，它是一种非常好的数据思维方式，并且在驯服一些让工程师夜不能寐的最广泛的问题时极为有用。那么，接下来我们来看看如何实现它。

# 在Python中的数据湖上实现WAP

我们不会进行关于WAP的理论讨论，也不会提供实现它的各种方式的详尽调查（[Alex Merced](https://www.linkedin.com/in/alexmerced/)来自[Dremio](https://www.dremio.com/)和[Einat Orr](https://www.linkedin.com/in/einat-orr-359ba6/)来自[LakeFs](https://lakefs.io/)已经做得非常出色）。相反，我们将提供一个关于数据湖中WAP的参考实现。

> 👉 **系好安全带，克隆** [Repo](https://github.com/BauplanLabs/no-jvm-wap-with-iceberg)**，然后试试看！**
> 
> 📌 **更多详情，请参考** [**README**](https://github.com/BauplanLabs/no-jvm-wap-with-iceberg/blob/main/README.md) **项目文档。**

# 架构和工作流

这里的想法是模拟一个数据摄取工作流，并通过分支数据湖来实现WAP模式，在决定是否将数据放入数据湖的最终表之前，先运行数据质量测试。

我们使用Nessie的分支功能来获取我们的沙箱环境，在该环境中，数据无法被下游消费者读取，同时使用AWS Lambda来运行WAP逻辑。

本质上，每当一个新的parquet文件上传时，一个Lambda函数会被触发，创建一个数据目录中的分支，并将数据追加到Iceberg表中。然后，使用PyIceberg执行一个简单的数据质量测试，检查表中的某一列是否包含NULL值。

**如果答案是肯定的，**则数据质量测试失败。新的分支将不会被合并到数据目录的主分支中，从而使数据无法在数据湖的主分支中被读取。相反，警报消息将被发送到 [Slack](https://slack.com/)。

**如果答案是否定的，**并且数据中不包含任何NULL值，那么数据质量测试通过。新的分支将被合并到数据目录的*main*分支中，数据将被追加到数据湖中的Iceberg表，以便其他流程读取。

![](../Images/0cec8ad1994f7ffe5eea60bbdbf027ea.png)

我们的WAP工作流：图来自作者

所有数据都是完全合成的，只需运行项目即可自动生成。当然，我们提供了选择是否生成符合数据质量规范的数据，或者生成包含NULL值的数据的可能性。

为了实现完整的端到端流程，我们将使用以下组件：

+   存储：[AWS S3](https://aws.amazon.com/s3/)

+   开放表格格式：[Apache Iceberg](https://iceberg.apache.org/)

+   数据目录：[Project Nessie](https://projectnessie.org/)

+   代码实现：[PyIceberg](https://py.iceberg.apache.org/)，[PyNessie](https://projectnessie.org/develop/python/)

+   无服务器运行时：[Lambda](https://aws.amazon.com/lambda/)

+   虚拟私人服务器：[Lightsail](https://aws.amazon.com/lightsail/)

+   警报系统：[Slack](https://slack.com/)

![](../Images/50bd27c1f3c729677ca7ab2ea24ac596.png)

项目架构：图来自作者

这个项目是相当自包含的，带有设置整个基础设施的脚本，因此只需要对AWS和Python有基础的了解即可。

这并不打算作为一个生产级的解决方案，而是作为一个参考实现，是更复杂场景的起点：代码非常详细且有大量注释，便于修改和扩展基本概念，以更好地适应任何人的用例。

# 可视化

为了可视化数据质量测试的结果，我们提供了一个非常简单的 [Streamlit](https://streamlit.io/) 应用，可以用来查看当一些新数据上传到 S3 上的第一个位置时发生了什么——该位置对下游消费者不可用。

我们可以使用该应用检查不同分支中的表格有多少行，对于 *main* 之外的分支，可以轻松地查看数据质量测试在哪一列和多少行中失败。

![](../Images/62d00f29924ccf899b8755c6a8e842ec.png)

数据质量应用——当你检查某个上传分支（即 *emereal-keen-shame*）时会看到这种情况，在该分支中，3000 行数据被附加进来，但由于 *my_col_1* 中的一个值为 NULL，数据质量检查未通过。图片来自作者。

# 从数据湖到 Lakehouse

一旦我们基于 Iceberg 构建了 WAP 流程，就可以利用它为下游消费者实现一个可组合的设计。在我们的仓库中，我们提供了 [Snowflake](https://www.snowflake.com/en/) 集成的说明，以便探索这一架构可能性。

![](../Images/b2208722df95aa1b1fbbeefe9c18dcc3.png)

向 Lakehouse 迈出的第一步：图片来自作者

这是 [**Lakehouse**](https://www.databricks.com/sites/default/files/2020/12/cidr_lakehouse.pdf) 架构的一个核心理念，它的设计目的是比现代数据仓库更灵活，同时比传统数据湖更易用。

一方面，Lakehouse 的核心是利用对象存储来消除数据冗余，并同时降低存储成本。另一方面，它应该提供更多灵活性，让用户能够为不同的目的选择不同的计算引擎。

所有这些听起来在理论上很有趣，但在大规模工程实践中似乎非常复杂。即使是 Snowflake 和 S3 桶作为外部卷的简单集成，说实话也相当繁琐。

> ***事实上，我们必须强调这一点，转向完整的 Lakehouse 架构需要大量的工作。真的很多工作！***

话虽如此，即使是千里之行，亦始于足下，那么为什么我们不从解决那些最简单但能带来实际影响的问题开始呢？

该仓库中的示例展示了其中一个简单的用例：WAP 和数据质量测试。这里的 WAP 模式提供了一个机会，将数据质量测试所需的计算（以及可能的一些摄取 ETL）**移出数据仓库**，同时仍然能够利用 Snowflake 对经过认证的制品进行更高价值的分析工作负载。我们希望这篇文章能够帮助开发者构建自己的概念验证并加以使用。

# 结论

这里提出的参考实现有几个优势：

## 表格比文件更好

数据湖在历史上一直很难开发，因为数据抽象与传统数据库中通常采用的方式非常不同。大数据框架如 [Spark](https://spark.apache.org/) 最早提供了处理存储为不同格式文件（例如 parquet、csv 等）的海量原始数据的能力，但人们通常并不以文件为单位进行思考：他们是以表格为单位思考的。

我们出于这个原因使用开放表格格式。Iceberg 将主要的数据湖抽象转换为表格，而不是文件，这使得操作变得更加直观。现在我们可以原生使用 SQL 查询引擎来探索数据，并且可以依赖 Iceberg 来提供正确的模式演进。

## 互操作性对我们有利

Iceberg 还从架构角度提供了更好的互操作性。使用开放表格格式的主要好处之一是数据可以保存在对象存储中，同时可以使用高性能的 SQL 引擎（如 Spark、[Trino](https://trino.io/)、[Dremio](https://www.dremio.com/)）和数据仓库（如 [Snowflake](https://www.snowflake.com/en/)、[Redshift](https://aws.amazon.com/redshift/)）进行查询。Iceberg 被大多数计算引擎支持这一事实，对我们如何构建数据平台有着深远的影响。

如上所述，我们建议的与 Snowflake 的集成旨在展示可以故意将用于摄取 ETL 和数据质量测试的计算移出数据仓库，并将数据仓库保留用于大规模分析作业和需要高性能的最后一公里查询。大规模应用时，这一理念可以转化为显著降低成本。

## 分支是有用的抽象

WAP 模式要求一种写入数据的位置，以避免消费者不小心读取。分支语义自然提供了一种实现此目标的方式，这也是我们使用 Nessie 在数据目录级别利用分支语义的原因。Nessie 基于 Iceberg 并依赖其时间旅行和表格分支功能。我们在代码库中做了大量工作，使得 Nessie 可以直接与 Python 配合使用。结果是，用户可以与 Nessie 目录交互，并在数据目录的不同分支中写入 Iceberg 表格，而无需依赖基于 JVM 的进程来执行写入。

## 更简洁的开发者体验

最后，完全基于 Python 的端到端体验显著简化了系统的设置和与之交互的过程。我们所知道的任何其他系统，都需要 JVM 或额外的托管服务来将数据写回到 Iceberg 表格中的不同分支，而在这个实现中，整个 WAP 逻辑可以在单个 lambda 函数内运行。

JVM本身并没有什么固有的缺陷。它是许多大数据框架的核心组件，提供了一个通用API，以便与平台特定资源进行交互，同时确保安全性和正确性。然而，从开发者体验的角度来看，JVM带来了一定的代价。任何与Spark打过交道的人都知道，基于JVM的系统往往脆弱，容易出现神秘的错误。对于许多从事数据工作并将Python视为其*通用语言*的人来说，JVM的优势是以可用性为代价的。

我们希望更多的人能像我们一样对可组合设计充满热情，我们希望像Iceberg和Arrow这样的开放标准能成为主流，但最重要的是，我们希望这对大家有所帮助。

就是这样。
