# 采用 Spark Connect

> 原文：[https://towardsdatascience.com/adopting-spark-connect-cdd6de69fa98?source=collection_archive---------7-----------------------#2024-11-07](https://towardsdatascience.com/adopting-spark-connect-cdd6de69fa98?source=collection_archive---------7-----------------------#2024-11-07)

## 我们如何使用共享的 Spark 服务器来提高 Spark 基础设施的效率

[](https://medium.com/@sergey.kotlov?source=post_page---byline--cdd6de69fa98--------------------------------)[![Sergey Kotlov](../Images/63dd13c266505832b4cd6242b75f4968.png)](https://medium.com/@sergey.kotlov?source=post_page---byline--cdd6de69fa98--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cdd6de69fa98--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cdd6de69fa98--------------------------------) [Sergey Kotlov](https://medium.com/@sergey.kotlov?source=post_page---byline--cdd6de69fa98--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cdd6de69fa98--------------------------------) ·15 分钟阅读·2024年11月7日

--

![](../Images/80211dfce51f1158143441e6eba80f48.png)

图片由[Kanenori](https://pixabay.com/users/kanenori-4749850/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5313115)提供，来自[Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5313115)

[Spark Connect](https://spark.apache.org/docs/latest/spark-connect-overview.html)是[Spark 生态系统](https://spark.apache.org/)中一个相对较新的组件，它允许轻量级客户端在远程 Spark 集群上运行 Spark 应用程序。这项技术可以为使用 DataFrame API 的 Spark 应用程序提供一些好处。Spark 长期以来就允许在远程 Thrift JDBC 服务器上运行 SQL 查询。然而，能够远程运行用任何支持的语言（如 Scala、Python）编写的客户端应用程序的能力，直到 Spark 3.4 版本才出现。

在本文中，我将分享我们使用 Spark Connect（版本 3.5）的经验。我将讨论我们获得的好处、与运行 Spark 客户端应用程序相关的技术细节，以及一些关于如何使您的 Spark Connect 设置更加高效和稳定的建议。

# 使用动机

Spark 是 Joom 分析平台的关键组成部分之一。我们有大量的内部用户和超过 1000 个自定义的 Spark 应用程序。这些应用程序在不同的时间运行，具有不同的复杂性，并且需要非常不同的计算资源（从几核几分钟到多达 250 核几个小时不等）。之前，所有应用程序都是作为独立的 Spark 应用程序执行的（每个应用程序都有自己的 driver 和 executor），这对于小型和中型应用程序（我们历史上有很多此类应用程序）来说，会导致明显的开销。引入 Spark Connect 后，现在可以设置一个共享的 Spark Connect 服务器，并在其上运行多个 Spark 客户端应用程序。从技术上讲，Spark Connect 服务器是一个包含 Spark Connect 端点的 Spark 应用程序。

![](../Images/0e9180f1354e8349d8b2b09848867414.png)

作者图片

下面是我们从中获得的好处：

+   资源节省

    - 通过 Spark Connect 运行时，客户端应用程序不需要自己的 Spark driver（通常使用超过 1.5 GB 的内存）。相反，它们使用的是一个轻量级客户端，典型的内存消耗为 200 MB。

    - 执行器利用率得到了改善，因为任何执行器都可以运行多个客户端应用程序的任务。例如，假设某个 Spark 应用程序在执行过程中，使用的核心数和内存显著少于最初请求的资源。出现这种情况的原因有很多。然后，在独立的 Spark 应用程序中，当前未使用的资源往往会被浪费，因为动态资源分配通常无法高效地缩减资源。然而，在 Spark Connect 服务器的情况下，释放的核心和内存可以立即用来运行其他客户端应用程序的任务。

+   启动等待时间减少

    - 出于各种原因，我们必须限制同时运行的独立 Spark 应用程序的数量，如果所有插槽都被占用，它们可能会在队列中等待相当长时间。这可能会对数据准备时间和用户体验产生负面影响。在 Spark Connect 服务器的情况下，我们目前能够避免这种限制，所有 Spark Connect 客户端应用程序在启动后都会立即开始运行。

    - 对于临时执行，最好尽可能减少获得结果的时间，避免让用户等待。在独立的 Spark 应用程序的情况下，启动客户端应用程序通常需要为其 driver 和 executor 配置额外的 EC2 节点，并初始化 driver 和 executor。所有这些加起来可能需要超过 4 分钟。而在 Spark Connect 服务器的情况下，至少其 driver 始终处于运行状态并准备接受请求，因此只需等待额外的执行器，且执行器通常已经可用。这可能大大减少临时应用程序准备就绪的等待时间。

## 我们的约束条件

目前，我们没有在 Spark Connect 上运行长时间运行的重型应用程序，原因如下：

+   它们可能会导致 Spark Connect 服务器的故障或不稳定行为（例如，通过溢出执行器节点上的磁盘）。这可能会导致整个平台的大规模问题。

+   它们通常需要独特的内存设置，并使用特定的优化技术（例如，自定义的 extraStrategies）。

+   我们目前面临一个问题，即给 Spark Connect 服务器分配大量执行器以处理非常大的并发负载（这与 Spark 任务调度器的行为相关，超出了本文的讨论范围）。

因此，重型应用程序仍然作为单独的 Spark 应用程序运行。

# 启动客户端应用程序

*我们使用* [*Spark on Kubernetes/EKS*](https://spark.apache.org/docs/latest/running-on-kubernetes.html) *和* [*Airflow*](https://airflow.apache.org/)*。一些代码示例将特定于这个环境。*

我们有太多不同且不断变化的 Spark 应用程序，需要花费太多时间手动确定每个应用程序是否应该根据我们的标准在 Spark Connect 上运行。此外，运行在 Spark Connect 上的应用程序列表需要定期更新。例如，假设今天某个应用程序足够轻，我们决定在 Spark Connect 上运行它。但明天，它的开发者可能会添加几个大的连接操作，使得它变得非常重。此时，最好将其作为单独的 Spark 应用程序来运行。反之的情况也是可能的。

最终，我们创建了一个服务来自动确定如何启动每个特定的客户端应用程序。该服务分析每个应用程序以前运行的历史记录，评估诸如`总任务时间`、`Shuffle 写入`、`磁盘溢出`等指标（这些数据是通过 [SparkListener](https://github.com/joomcode/spark-platform/blob/main/lib/src/main/scala/com/joom/spark/monitoring/StatsReportingSparkListener.scala) 收集的）。开发者为应用程序设置的自定义参数（例如，驱动程序和执行器的内存设置）也会被考虑在内。根据这些数据，服务会自动确定每个应用程序此次是否应该在 Spark Connect 服务器上运行，或作为单独的 Spark 应用程序运行。因此，我们所有的应用程序都应该准备好以这两种方式之一运行。

在我们的环境中，每个客户端应用程序都是独立构建的，并拥有包含应用程序代码以及特定依赖项的 JAR 文件（例如，ML 应用程序通常会使用像 CatBoost 等第三方库）。问题在于，Spark Connect 的 SparkSession API 与独立 Spark 应用程序所使用的 SparkSession API 略有不同（Spark Connect 客户端使用 `spark-connect-client-jvm` 组件）。因此，我们必须在每个客户端应用程序的构建阶段确定它是否会通过 Spark Connect 运行。然而，我们并不知道这一点。以下将描述我们启动客户端应用程序的方法，该方法避免了为同一个应用程序构建和管理两个版本的 JAR 工件。

对于每个 Spark 客户端应用程序，我们只构建一个 JAR 文件，其中包含应用程序代码和特定的依赖项。这个 JAR 文件既用于 Spark Connect 运行模式，也用于作为独立的 Spark 应用程序运行。因此，这些客户端 JAR 文件不包含特定的 Spark 依赖项。适当的 Spark 依赖项（如 `spark-core`/`spark-sql` 或 `spark-connect-client-jvm`）会在后续通过 Java 类路径提供，具体取决于运行模式。无论如何，所有客户端应用程序都使用相同的 Scala 代码来初始化 SparkSession，这一操作会根据运行模式进行调整。所有客户端应用程序的 JAR 文件都针对常规的 Spark API 进行构建。因此，在面向 Spark Connect 客户端的代码部分，`SparkSession` 方法（如 `remote`、`addArtifact`）会通过反射来调用：

```py
val sparkConnectUri: Option[String] = Option(System.getenv("SPARK_CONNECT_URI"))

val isSparkConnectMode: Boolean = sparkConnectUri.isDefined

def createSparkSession(): SparkSession = {
  if (isSparkConnectMode) {
    createRemoteSparkSession()
  } else {
    SparkSession.builder
      // Whatever you need to do to configure SparkSession for a separate 
      // Spark application.
      .getOrCreate
  }
}

private def createRemoteSparkSession(): SparkSession = {
  val uri = sparkConnectUri.getOrElse(throw new Exception(
    "Required environment variable 'SPARK_CONNECT_URI' is not set."))

  val builder = SparkSession.builder
  // Reflection is used here because the regular SparkSession API does not 
  // contain these methods. They are only available in the SparkSession API 
  // version for Spark Connect.
  classOf[SparkSession.Builder]
    .getDeclaredMethod("remote", classOf[String])
    .invoke(builder, uri)

  // A set of identifiers for this application (to be used later).
  val scAppId = s"spark-connect-${UUID.randomUUID()}"
  val airflowTaskId = Option(System.getenv("AIRFLOW_TASK_ID"))
    .getOrElse("unknown_airflow_task_id")
  val session = builder
    .config("spark.joom.scAppId", scAppId)
    .config("spark.joom.airflowTaskId", airflowTaskId)
    .getOrCreate()

  // If the client application uses your Scala code (e.g., custom UDFs), 
  // then you must add the jar artifact containing this code so that it 
  // can be used on the Spark Connect server side.
  val addArtifact = Option(System.getenv("ADD_ARTIFACT_TO_SC_SESSION"))
    .forall(_.toBoolean)

  if (addArtifact) {
    val mainApplicationFilePath = 
      System.getenv("SPARK_CONNECT_MAIN_APPLICATION_FILE_PATH")
    classOf[SparkSession]
      .getDeclaredMethod("addArtifact", classOf[String])
      .invoke(session, mainApplicationFilePath)
  }

  Runtime.getRuntime.addShutdownHook(new Thread() {
    override def run(): Unit = {
      session.close()
    }
  })

  session
}
```

在 Spark Connect 模式下，这段客户端代码可以作为常规的 Java 应用程序在任何地方运行。由于我们使用 Kubernetes，这段代码会在 Docker 容器中运行。所有与 Spark Connect 相关的依赖都打包进一个 Docker 镜像，用来运行客户端应用程序（该镜像的一个简化示例可以在[这里](https://github.com/kotlovs/spark-connect-examples/tree/main/spark-connect-client-image)找到）。该镜像不仅包含 `spark-connect-client-jvm` 组件，还包括几乎所有客户端应用程序常用的其他依赖（例如，`hadoop-aws`，因为我们几乎总是在客户端与 S3 存储进行交互）。

```py
FROM openjdk:11-jre-slim

WORKDIR /app

# Here, we copy the common artifacts required for any of our Spark Connect 
# clients (primarily spark-connect-client-jvm, as well as spark-hive, 
# hadoop-aws, scala-library, etc.).
COPY build/libs/* /app/

COPY src/main/docker/entrypoint.sh /app/
RUN chmod +x ./entrypoint.sh
ENTRYPOINT ["./entrypoint.sh"]
```

这个通用 Docker 镜像用于通过 Spark Connect 运行我们的所有客户端应用程序。同时，它不包含特定应用程序的客户端 JAR 文件及其依赖项，因为有许多此类应用程序，它们不断更新，并可能依赖任何第三方库。相反，当启动某个特定的客户端应用程序时，会通过环境变量传递该应用程序 JAR 文件的位置，在初始化过程中（在 `entrypoint.sh` 中）下载该 JAR 文件：

```py
#!/bin/bash
set -eo pipefail

# This variable will also be used in the SparkSession builder within 
# the application code.
export SPARK_CONNECT_MAIN_APPLICATION_FILE_PATH="/tmp/$(uuidgen).jar"

# Download the JAR with the code and specific dependencies of the client 
# application to be run. All such JAR files are stored in S3, and when 
# creating a client Pod, the path to the required JAR is passed to it 
# via environment variables.
java -cp "/app/*" com.joom.analytics.sc.client.S3Downloader \ 
    ${MAIN_APPLICATION_FILE_S3_PATH} ${SPARK_CONNECT_MAIN_APPLICATION_FILE_PATH}

# Launch the client application. Any MAIN_CLASS initializes a SparkSession 
# at the beginning of its execution using the code provided above.
java -cp ${SPARK_CONNECT_MAIN_APPLICATION_FILE_PATH}:"/app/*" ${MAIN_CLASS} "$@"
```

最后，当启动应用程序时，我们的自定义 SparkAirflowOperator 会根据此应用程序先前运行的统计数据自动确定执行模式（Spark Connect 或单独的模式）。

+   在 Spark Connect 的情况下，我们使用 [KubernetesPodOperator](https://airflow.apache.org/docs/apache-airflow-providers-cncf-kubernetes/stable/operators.html) 来启动应用程序的客户端 Pod。`KubernetesPodOperator` 需要作为参数提供前面描述的 Docker 镜像，以及环境变量（`MAIN_CLASS`、`JAR_PATH` 等），这些变量将在 `entrypoint.sh` 和应用程序代码中使用。无需为客户端 Pod 分配过多资源（例如，在我们的环境中，它的典型消耗：内存 — 200 MB，vCPU — 0.15）。

+   在单独的 Spark 应用程序中，我们使用自定义的 AirflowOperator，通过 [spark-on-k8s-operator](https://github.com/kubeflow/spark-operator) 和官方的 [Spark Docker 镜像](https://spark.apache.org/docs/latest/running-on-kubernetes.html#docker-images) 运行 Spark 应用程序。我们暂时跳过有关 Spark AirflowOperator 的详细内容，因为它是一个庞大的话题，值得单独撰写一篇文章。

# 与常规 Spark 应用程序的兼容性问题

不是所有现有的 Spark 应用程序都可以在 Spark Connect 上成功执行，因为其 SparkSession API 与用于单独 Spark 应用程序的 SparkSession API 不同。例如，如果你的代码使用了 `sparkSession.sparkContext` 或 `sparkSession.sessionState`，它将在 Spark Connect 客户端中失败，因为 Spark Connect 版本的 SparkSession 不具备这些属性。

在我们的案例中，最常见的问题原因是使用了 `sparkSession.sessionState.catalog` 和 `sparkSession.sparkContext.hadoopConfiguration`。在某些情况下，`sparkSession.sessionState.catalog` 可以替换为 `sparkSession.catalog`，但并非总是如此。如果客户端执行的代码涉及对数据存储的操作，则可能需要 `sparkSession.sparkContext.hadoopConfiguration`，例如：

```py
def delete(path: Path, recursive: Boolean = true)
          (implicit hadoopConfig: Configuration): Boolean = {
  val fs = path.getFileSystem(hadoopConfig)
  fs.delete(path, recursive)
}
```

幸运的是，可以为 Spark Connect 客户端创建一个独立的 `SessionCatalog`。在这种情况下，Spark Connect 客户端的类路径还必须包含 `org.apache.spark:spark-hive_2.12`，以及与存储交互的库（因为我们使用 S3，所以在我们的案例中是 `org.apache.hadoop:hadoop-aws`）。

```py
import org.apache.spark.SparkConf
import org.apache.hadoop.conf.Configuration
import org.apache.spark.sql.hive.StandaloneHiveExternalCatalog
import org.apache.spark.sql.catalyst.catalog.{ExternalCatalogWithListener, SessionCatalog}

// This is just an example of what the required properties might look like. 
// All of them should already be set for existing Spark applications in one 
// way or another, and their complete list can be found in the UI of any
// running separate Spark application on the Environment tab.
val sessionCatalogConfig = Map(
  "spark.hadoop.hive.metastore.uris" -> "thrift://metastore.spark:9083",
  "spark.sql.catalogImplementation" -> "hive",
  "spark.sql.catalog.spark_catalog" -> "org.apache.spark.sql.delta.catalog.DeltaCatalog",
)

val hadoopConfig = Map(
  "hive.metastore.uris" -> "thrift://metastore.spark:9083",
  "fs.s3.impl" -> "org.apache.hadoop.fs.s3a.S3AFileSystem",
  "fs.s3a.aws.credentials.provider" -> "com.amazonaws.auth.DefaultAWSCredentialsProviderChain",
  "fs.s3a.endpoint" -> "s3.amazonaws.com",
  // and others...
)

def createStandaloneSessionCatalog(): (SessionCatalog,  Configuration) = {
  val sparkConf = new SparkConf().setAll(sessionCatalogConfig)
  val hadoopConfiguration = new Configuration()
  hadoopConfig.foreach { 
    case (key, value) => hadoopConfiguration.set(key, value) 
  }

  val externalCatalog = new StandaloneHiveExternalCatalog(
    sparkConf, hadoopConfiguration)
  val sessionCatalog = new SessionCatalog(
    new ExternalCatalogWithListener(externalCatalog)
  )
  (sessionCatalog, hadoopConfiguration)
}
```

你还需要为 `HiveExternalCatalog` 创建一个封装器，使其可以在代码中访问（因为 `HiveExternalCatalog` 类是 `org.apache.spark` 包私有的）：

```py
package org.apache.spark.sql.hive

import org.apache.hadoop.conf.Configuration
import org.apache.spark.SparkConf

class StandaloneHiveExternalCatalog(conf: SparkConf, hadoopConf: Configuration) 
  extends HiveExternalCatalog(conf, hadoopConf)
```

此外，通常可以用替代代码来替换 Spark Connect 上无法运行的代码，例如：

+   `sparkSession.createDataFrame(sparkSession.sparkContext.parallelize(data), schema)` ==> `sparkSession.createDataFrame(data.toList.asJava, schema)`

+   `sparkSession.sparkContext.getConf.get(“some_property”)` ==> `sparkSession.conf.get(“some_property”)`

## 回退到独立的 Spark 应用程序

不幸的是，修复特定的 Spark 应用程序使其作为 Spark Connect 客户端工作并不总是容易。例如，项目中使用的第三方 Spark 组件存在很大风险，因为它们通常在编写时没有考虑与 Spark Connect 的兼容性。由于在我们的环境中，任何 Spark 应用程序都可以自动启动在 Spark Connect 上，因此我们认为在失败时实现回退为独立的 Spark 应用程序是合理的。简化后的逻辑如下：

+   如果某个应用程序在 Spark Connect 上失败，我们会立即尝试将其重新作为独立的 Spark 应用程序运行。同时，我们会增加在 Spark Connect 上执行过程中发生的失败次数计数器（每个客户端应用程序都有自己的计数器）。

+   下次启动此应用程序时，我们检查该应用程序的失败计数器：

    - 如果失败次数少于 3 次，我们假设上次应用程序失败并非由于与 Spark Connect 不兼容，而是由于其他任何可能的临时原因。因此，我们会尝试再次在 Spark Connect 上运行它。如果这次成功完成，则该客户端应用程序的失败计数器会被重置为零。

    - 如果已经发生了 3 次失败，我们假设该应用程序无法在 Spark Connect 上工作，并且暂时停止在 Spark Connect 上运行它。之后，它将只作为独立的 Spark 应用程序启动。

+   如果应用程序在 Spark Connect 上出现了 3 次失败，但最后一次失败发生在 2 个月以上之前，我们会尝试再次在 Spark Connect 上运行它（以防在此期间发生了变化，使其与 Spark Connect 兼容）。如果这次成功，我们会再次将失败计数器重置为零。如果再次失败，下次尝试将在 2 个月后进行。

这种方法比维护一段代码来识别失败原因（从日志中获取）要简单一些，并且在大多数情况下效果很好。尝试在 Spark Connect 上运行不兼容的应用程序通常不会带来显著的负面影响，因为在绝大多数情况下，如果应用程序与 Spark Connect 不兼容，它会在启动后立即失败，且不会浪费时间和资源。然而，值得一提的是，我们所有的应用程序都是幂等的。

# 统计数据收集

正如我之前提到的，我们会收集每个 Spark 应用程序的统计数据（我们平台的大多数优化和警报都依赖于这些数据）。当应用程序作为独立的 Spark 应用程序运行时，这很容易实现。而在 Spark Connect 的情况下，每个客户端应用程序的阶段和任务需要与所有其他在共享 Spark Connect 服务器中同时运行的客户端应用程序的阶段和任务区分开。

你可以通过为客户端 `SparkSession` 设置自定义属性，将任何标识符传递给 Spark Connect 服务器：

```py
val session = builder
  .config("spark.joom.scAppId", scAppId)
  .config("spark.joom.airflowTaskId", airflowTaskId)
  .getOrCreate()
```

然后，在 Spark Connect 服务器端的 `SparkListener` 中，你可以检索所有传递的信息，并将每个阶段/任务与特定的客户端应用程序关联起来。

```py
class StatsReportingSparkListener extends SparkListener {

  override def onStageSubmitted(stageSubmitted: SparkListenerStageSubmitted): Unit = {
    val stageId = stageSubmitted.stageInfo.stageId
    val stageAttemptNumber = stageSubmitted.stageInfo.attemptNumber()
    val scAppId = stageSubmitted.properties.getProperty("spark.joom.scAppId")
    // ...
  }
}
```

[在这里，你可以找到我们用来收集统计数据的 `StatsReportingSparkListener` 代码](https://github.com/joomcode/spark-platform/blob/main/lib/src/main/scala/com/joom/spark/monitoring/StatsReportingSparkListener.scala)。你也许对 [这个免费的工具](https://cloud.joom.ai/) 感兴趣，它可以帮助你发现 Spark 应用程序中的性能问题。

# 优化与稳定性提升

Spark Connect 服务器是一个持续运行的 Spark 应用程序，多个客户端可以在其上运行作业。因此，定制其 [属性](https://spark.apache.org/docs/latest/configuration.html) 使其更可靠并防止资源浪费可能是值得的。以下是我们案例中证明有效的一些设置：

```py
// Using dynamicAllocation is important for the Spark Connect server 
// because the workload can be very unevenly distributed over time.
spark.dynamicAllocation.enabled: true  // default: false

// This pair of parameters is responsible for the timely removal of idle 
// executors:
spark.dynamicAllocation.cachedExecutorIdleTimeout: 5m  // default: infinity
spark.dynamicAllocation.shuffleTracking.timeout: 5m  // default: infinity

// To create new executors only when the existing ones cannot handle 
// the received tasks for a significant amount of time. This allows you 
// to save resources when a small number of tasks arrive at some point 
// in time, which do not require many executors for timely processing. 
// With increased schedulerBacklogTimeout, unnecessary executors do not 
// have the opportunity to appear by the time all incoming tasks are 
// completed. The time to complete the tasks increases slightly with this, 
// but in most cases, this increase is not significant.
spark.dynamicAllocation.schedulerBacklogTimeout: 30s  // default: 1s

// If, for some reason, you need to stop the execution of a client 
// application (and free up resources), you can forcibly terminate the client. 
// Currently, even explicitly closing the client SparkSession does not 
// immediately end the execution of its corresponding Jobs on the server. 
// They will continue to run for a duration equal to 'detachedTimeout'. 
// Therefore, it may be reasonable to reduce it.
spark.connect.execute.manager.detachedTimeout: 2m  // default: 5m

// We have encountered a situation when killed tasks may hang for 
// an unpredictable amount of time, leading to bad consequences for their 
// executors. In this case, it is better to remove the executor on which 
// this problem occurred.
spark.task.reaper.enabled: true // default: false
spark.task.reaper.killTimeout: 300s  // default: -1

// The Spark Connect server can run for an extended period of time. During 
// this time, executors may fail, including for reasons beyond our control 
// (e.g., AWS Spot interruptions). This option is needed to prevent 
// the entire server from failing in such cases.
spark.executor.maxNumFailures: 1000

// In our experience, BroadcastJoin can lead to very serious performance 
// issues in some cases. So, we decided to disable broadcasting. 
// Disabling this option usually does not result in a noticeable performance 
// degradation for our typical applications anyway.
spark.sql.autoBroadcastJoinThreshold: -1 // default: 10MB

// For many of our client applications, we have to add an artifact to 
// the client session (method sparkSession.addArtifact()). 
// Using 'useFetchCache=true' results in double space consumption for 
// the application JAR files on executors' disks, as they are also duplicated 
// in a local cache folder. Sometimes, this even causes disk overflow with 
// subsequent problems for the executor.
spark.files.useFetchCache: false   // default: true

// To ensure fair resource allocation when multiple applications are 
// running concurrently.
spark.scheduler.mode: FAIR  // default: FIFO
```

例如，在我们调整了 `idle timeout` 属性后，资源利用率发生了如下变化：

![](../Images/ea2bebad36ec8b519bfb5cfc004c4e11.png)

图片来源：作者

## 预防性重启

在我们的环境中，Spark Connect 服务器（版本 3.5）在连续运行几天后可能会变得不稳定。最常见的情况是，我们面临客户端应用程序任务随机挂起且时间无限，但也可能会遇到其他问题。此外，随着时间的推移，整个 Spark Connect 服务器随机失败的概率会急剧增加，这可能发生在不合适的时刻。

随着该组件的发展，它可能会变得更加稳定（或者我们会发现我们的 Spark Connect 设置中存在问题）。但目前，最简单的解决方案是每天在合适的时刻（即没有客户端应用程序在运行时）对 Spark Connect 服务器进行预防性重启。重启代码的示例 [可以在这里找到](https://gist.github.com/kotlovs/437809684d7ebe3b7a93a1af804def8c)。

# 结论

在本文中，我描述了我们使用 Spark Connect 运行大量不同类型 Spark 应用程序的经验。

总结上述内容：

+   该组件有助于节省资源并减少 Spark 客户端应用程序执行的等待时间。

+   在共享 Spark Connect 服务器上运行哪些应用程序需要谨慎，因为资源密集型应用程序可能会对整个系统造成问题。

+   你可以创建一个用于启动客户端应用程序的基础设施，以便在启动时自动决定如何运行任何应用程序（是作为独立的 Spark 应用程序还是作为 Spark Connect 客户端）。

+   需要注意的是，并非所有应用程序都能够在 Spark Connect 上运行，但此类情况的数量可以显著减少。如果有可能运行未经过 Spark Connect 版本 SparkSession API 兼容性测试的应用程序，建议实现回退机制以运行独立的 Spark 应用程序。

+   值得注意的是，有一些 Spark 属性可以提高资源利用率并增加 Spark Connect 服务器的整体稳定性。可能还需要合理设置 Spark Connect 服务器的定期预防性重启，以减少意外故障和不希望出现的行为的发生概率。

总的来说，我们在公司使用 Spark Connect 的体验是积极的。我们将继续密切关注这项技术的发展，并计划扩大其使用范围。
