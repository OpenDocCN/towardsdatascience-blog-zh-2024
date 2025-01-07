# 如何将 Apache Flink、Kafka 和 PostgreSQL Docker 化，实现实时数据流处理

> 原文：[https://towardsdatascience.com/how-i-dockerized-apache-flink-kafka-and-postgresql-for-real-time-data-streaming-c4ce38598336?source=collection_archive---------1-----------------------#2024-06-19](https://towardsdatascience.com/how-i-dockerized-apache-flink-kafka-and-postgresql-for-real-time-data-streaming-c4ce38598336?source=collection_archive---------1-----------------------#2024-06-19)

## 使用 Docker 集成 pyFlink、Kafka 和 PostgreSQL

[](https://adenevreze.medium.com/?source=post_page---byline--c4ce38598336--------------------------------)[![Augusto de Nevrezé](../Images/bd7d6509149ddb447dd7e5af9f09e4b1.png)](https://adenevreze.medium.com/?source=post_page---byline--c4ce38598336--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c4ce38598336--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c4ce38598336--------------------------------) [Augusto de Nevrezé](https://adenevreze.medium.com/?source=post_page---byline--c4ce38598336--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c4ce38598336--------------------------------) ·阅读时间：10分钟·2024年6月19日

--

![](../Images/11d218990f84e7153933c512352bd704.png)

使用 Docker 准备你的 pyFlink 应用程序 — 作者使用 [https://www.dall-efree.com/](https://www.dall-efree.com/) 生成的图片

# 为什么要阅读这篇文章？

+   **真实世界的洞察**：从我个人克服集成难题的经历中获取实用的建议。

+   **完整设置**：学习如何使用 Docker-Compose 无缝集成 Flink、Kafka 和 PostgreSQL。

+   **逐步指南**：非常适合初学者和有经验的开发者，帮助你简化数据流处理架构。

# 设置场景

我开始了一个任务，旨在使用 Docker 集成 Apache Flink、Kafka 和 PostgreSQL。这项工作尤其令人兴奋，因为我使用了 pyFlink —— Flink 的 Python 版本 —— 它既强大又相对罕见。这个设置的目标是高效地处理和存储实时数据。在接下来的部分，我将展示我是如何实现这一目标的，讨论我遇到的挑战以及如何克服它们。最后，我将提供一个逐步指南，帮助你自己搭建并实验这个数据流管道。  

我们将要构建的基础设施如下所示。从外部来看，有一个发布者模块，用于模拟物联网传感器消息，类似于[之前的文章](https://medium.com/dev-genius/detecting-iot-alerts-with-apache-flink-7a2be19ad9dd)中讨论的内容。在 Docker 容器内部，我们将创建两个 Kafka 主题。第一个主题 *sensors* 用于实时存储来自物联网设备的消息。Flink 应用程序将从该主题消费消息，过滤出温度高于30°C的消息，并将其发布到第二个主题 *alerts*。此外，Flink 应用程序还将把消费的消息插入到专门为此目的创建的 PostgreSQL 表中。这个设置使我们能够以结构化的表格格式持久化传感器数据，提供进一步转化和分析的机会。像 Tableau 或 Power BI 这样的可视化工具可以连接到这些数据，用于实时绘图和仪表盘展示。

此外，alerts 主题可以被其他客户端消费，以根据其包含的消息启动相应的动作，例如启用空调系统或触发消防安全协议。

![](../Images/a1ad055bc5105bf1c170c1d526b9702c.png)

docker 容器中包含的服务 —— 图片由作者提供

为了跟随本教程，你可以克隆以下[仓库](https://github.com/augustodn/pyflink-docker)。项目根目录中包含一个 docker-compose.yml 文件，便于你初始化多容器应用程序。此外，你可以在 README 文件中找到详细的说明。

## docker-compose.yml 中 Kafka 端口的问题

最初，在使用 confluentinc Kafka Docker 镜像时，我遇到了 Kafka 端口配置的问题，这是这种设置的常见选择。通过日志可以明显看到这个问题，突显出在初始设置和故障排除阶段，不应该以分离模式（-d）运行 docker-compose up 的重要性。

失败的原因是内部和外部主机使用了相同的端口，导致了连接问题。我通过将内部端口更改为19092来解决了这个问题。我发现[这篇](https://www.confluent.io/blog/kafka-client-cannot-connect-to-broker-on-aws-on-docker-etc/)博客文章对问题的解释相当清晰。

```py
KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:19092,PLAINTEXT_HOST://localhost:9092
```

## 配置 Flink 会话模式

要在[会话模式](https://nightlies.apache.org/flink/flink-docs-master/docs/deployment/overview/#session-mode)下运行 Flink（允许在一个集群中运行多个作业），我在 docker-compose.yml 文件中使用了以下指令。

## PyFlink 的自定义 Docker 镜像

鉴于默认的 Apache Flink Docker 镜像不包括 Python 支持，我为 pyFlink 创建了一个[自定义 Docker 镜像](https://nightlies.apache.org/flink/flink-docs-master/docs/deployment/resource-providers/standalone/docker/#using-flink-python-on-docker)。这个自定义镜像确保 Flink 可以运行 Python 作业，并包含与 Kafka 和 PostgreSQL 集成所需的依赖项。用于创建此镜像的 Dockerfile 位于 pyflink 子目录中。

1.  **基础镜像**：我们从官方 Flink 镜像开始。

1.  **Python 安装**：安装了 Python 和 pip，并将 pip 升级到最新版本。

1.  **依赖管理**：通过 requirements.txt 安装依赖项。或者，可以将某些行注释掉，演示如何从本地文件手动安装依赖项，这对于在没有互联网访问的环境中部署很有用。

1.  **连接器库**：Kafka 和 PostgreSQL 的连接器会直接下载到 Flink 的 lib 目录中。这使得 Flink 在作业执行过程中能够与 Kafka 和 PostgreSQL 进行交互。

1.  **脚本复制**：从代码库中的脚本会复制到 /opt/flink 目录，由 Flink 任务管理器执行。

使用这个自定义 Docker 镜像，我们确保 pyFlink 可以在 Docker 容器中正常运行，并配备了与 Kafka 和 PostgreSQL 无缝交互所需的库。这种方法提供了灵活性，适用于开发和生产环境。

**注意：** 请确保根据您的部署环境的政策，处理下载连接器和其他依赖项时的网络或安全性问题。

## 集成 PostgreSQL

要将 Apache Flink 连接到 PostgreSQL 数据库，必须使用适当的 JDBC 连接器。pyFlink 的自定义 Docker 镜像会下载与 PostgreSQL 16 兼容的 PostgreSQL JDBC 连接器。

为了简化这一过程，代码库中包含一个 download_libs.sh 脚本，模拟了在 Flink Docker 容器中执行的操作。该脚本自动下载所需的库，确保 Docker 和本地环境之间的一致性。

**注意：** 连接器通常有两个版本。在这种情况下，由于我使用的是最新的稳定版本 Flink 1.18，我下载了 3.1.2–1.18 版本。我的猜测是，第一个版本跟踪了多个数据库的 JDBC 实现。它们可以在 [maven 目录](https://mvnrepository.com/artifact/org.apache.flink/flink-connector-jdbc/3.1.2-1.18)中找到。

```py
env.add_jars(
  f"file://{current_dir}/flink-connector-jdbc-3.1.2–1.18.jar",
  f"file://{current_dir}/postgresql-42.7.3.jar"
)
```

**定义 JDBC Sink**

在我们的 Flink 任务中，有一个至关重要的函数，名为 `configure_postgre_sink`，位于 `usr_jobs/postgres_sink.py` 文件中。这个函数负责配置一个通用的 PostgreSQL 接收器。为了有效使用它，你需要提供 SQL 数据操作语言（DML）语句和相应的值类型。用于流数据的类型定义为 `TYPE_INFO`... 我花了些时间才找出正确的声明方式 😅。

还需要注意的是，JdbcSink 有一个可选参数，用于定义 `ExecutionOptions`。在这个特定的例子中，我将使用 1 秒的更新间隔，并将行数限制为 200。你可以在[官方文档](https://nightlies.apache.org/flink/flink-docs-master/docs/connectors/datastream/jdbc/#jdbc-execution-options)中找到更多信息。是的，你猜对了，由于我定义了一个间隔，这可以视为一个微批次 ETL。然而，由于 Flink 的并行性，你可以在一个简单的脚本中同时处理多个数据流，而且它非常易于理解。

**注意：** 别忘了在 Postgres 中创建 `raw_sensors_data` 表，这是接收来自 IoT 传感器的原始数据的地方。这个步骤在下面的逐步指南中已经涵盖。

## 将数据传输到 Kafka

我在[之前的讨论](https://medium.com/dev-genius/detecting-iot-alerts-with-apache-flink-7a2be19ad9dd)中介绍了如何从 Kafka 主题消费数据。然而，我还没有配置接收器，接下来我们就会配置。这个配置有一些复杂之处，它是定义在一个函数中的，类似于 Postgres 接收器。此外，在将数据流传输到 Kafka 之前，你必须定义数据流的类型。请注意，`alarms_data` 流在传输到 Kafka 之前已经正确地转换为字符串，`output_type=Types.STRING()`，因为我已经将序列化器声明为 `SimpleStringSchema()`。

接下来的步骤我将展示如何从 `alerts` 主题获取数据。

## 本地或容器化配置

这个 Docker 配置的一个最大优点是，你可以在本地或容器内部以托管任务的方式运行 Flink。下图展示了本地 Flink 设置，你可以看到我们的 Flink 应用程序与 Docker 容器是分离的。这有助于排查 Flink 的问题，因为 Flink 本身并没有很好的本地可观察工具。实际上，我们想尝试一下[datorios](https://datorios.com/)提供的 Flink 工具，它们在监控方面非常有前景。

![](../Images/6d8993906980fe9862badc9ecdcad799.png)

在本地运行 Flink 应用程序，并且容器内部还运行着其他服务 — 图像来自作者

如果你想在本地尝试 Flink 应用程序，你需要正确地定义脚本所使用的主机和端口，这实际上是 `usr_jobs/postgres_sink.py` 文件中的两个常量：

容器运行时，使用：

```py
KAFKA_HOST = "kafka:19092"
POSTGRES_HOST = "postgres:5432"
```

本地运行时，使用：

```py
KAFKA_HOST = "localhost:9092"
POSTGRES_HOST = "localhost:5432"
```

默认情况下，仓库设置Flink应用程序在容器内运行。你可以通过Web UI监控正在运行的作业，访问[http://localhost:8081](http://localhost:8081)。如果你选择在本地运行作业，则无法查看。

![](../Images/add4733c451e9d50906584d638693caf.png)

显示Flink Web UI中运行作业的截图——作者提供的图片

**注意**：如果在本地运行作业，则需要安装位于requirements.txt中的Flink依赖项。如果你想使用poetry设置环境，还提供了一个pyproject.toml文件。

# 分步指南：运行流处理管道

## 第1步：启动多容器应用程序

通过运行docker-compose启动容器。我更倾向于不使用分离模式，以便在容器启动并运行时查看日志。

```py
docker-compose up
```

检查日志以查看服务是否正常运行。

## 第2步：创建Kafka主题

接下来，我们将创建主题以接收来自IoT传感器的数据，并存储Flink应用程序过滤后的警报。

```py
docker-compose exec kafka kafka-topics \
 -- create - topic sensors \
 -- bootstrap-server localhost:9092 \
 -- partitions 1 \
 -- replication-factor 1

docker-compose exec kafka kafka-topics \
 -- create - topic alerts \
 -- bootstrap-server localhost:9092 \
 -- partitions 1 \
 -- replication-factor 1
```

要检查主题是否正确创建，可以执行以下命令

```py
docker-compose exec kafka kafka-topics \
 -- bootstrap-server localhost:9092 \
 -- list
```

## 第3步：创建Postgres表

登录到Postgres控制台

```py
psql -h localhost -U flinkuser -d flinkdb
```

输入密码flinkpassword登录到Postgres控制台，请记住这是本地配置，因此默认访问权限已在docker-compose.yml中配置。然后创建表格

```py
CREATE TABLE raw_sensors_data (
message_id VARCHAR(255) PRIMARY KEY,
sensor_id INT NOT NULL,
message TEXT NOT NULL,
timestamp TIMESTAMPTZ NOT NULL
);
```

你可以通过以下方式检查表是否正确创建

```py
flinkdb=# \d raw_sensors_data
```

这将显示类似以下的结果：

![](../Images/d60419e34e6e3d6595fb7dabb57d16ff.png)

## 第4步：启动Kafka生产者

使用conda或poetry创建一个本地环境并安装python kafka包：

```py
pip install kafka-python
```

然后执行Kafka生产者，它模拟IoT传感器消息并将消息发布到传感器主题。

```py
python pyflink/usr_jobs/kafka_producer.py
```

让它在接下来的教程中一直运行。

## 第5步：初始化Flink任务

我们将从容器内启动Flink应用程序，因此你可以通过Web UI通过localhost:8081监控它。从仓库根目录运行以下命令：

```py
docker-compose exec flink-jobmanager flink run \
  -py /opt/flink/usr_jobs/postgres_sink.py
```

你将看到一些日志信息，此外，警报也会显示在flink-jobmanager容器的日志中。同时，你可以从Flink Web UI [http://localhost:8081/#/job/running](http://localhost:8081/#/job/running)检查作业是否正在运行。

![](../Images/61e46067d510dfa78e078de834b3ad0a.png)

运行作业的详细信息——作者提供的图片

显然，监控显示Flink作业中没有消息流动，但这不是真的，因为可以在docker日志中看到警报。

![](../Images/a76525be2f6b2ec01ef7e1d6d89b2518.png)

我们将通过Postgres表检查消息，并读取为此目的创建的警报主题。

## 第6步：读取Kafka主题中的警报

要读取警报主题中的数据，可以执行以下命令：

```py
docker-compose exec kafka kafka-console-consumer \
  -- bootstrap-server localhost:9092 \
  -- topic alerts \
  -- from-beginning
```

这将展示到目前为止该主题接收到的所有消息。

## 第7步：从Postgres表读取原始数据

此外，你还可以查询 IoT 传感器的原始消息，甚至在 PostgreSQL 中解析 JSON 数据：

```py
SELECT
  *,
  (message::json->>'temperature')::numeric as temperature
FROM raw_sensors_data
LIMIT 10;
```

## 第 8 步：停止服务

你可以通过在 Docker 终端按 Ctrl-c 来轻松停止所有服务。如果你更倾向于正确地关闭服务，可以按照以下步骤进行操作：

1.  在 Web UI 的作业详情页右上角点击以取消 Flink 作业。

1.  停止本地运行的 kafka_producer.py 脚本。

1.  在 Docker 终端按 Ctrl-c 停止服务

会话中交换的信息，在服务运行期间会被永久存储。因此，如果你想查询 Postgres 表或 Kafka 主题，数据将会在其中。

# 使用多个 Sink 在 PyFlink 作业中的见解

在用于演示的 Flink 作业中，我同时管理 2 个数据流，它们在同一个任务中运行。一个是写入来自传感器主题（IoT 设备）的原始数据，另一个是过滤后的警报数据，写入另一个主题。这种方式有一些优点和缺点，简单总结如下：

**单一作业使用多个 Sink 的优点：**

- 资源管理的简便性。

- 数据流的一致性。

**单一作业的缺点：**

- 随着逻辑的增长，可能变得复杂。

- 可扩展性可能是个问题。

**多个作业的优点：**

- 更好的故障隔离。

- 聚焦优化。

**多个作业的缺点：**

- 资源开销。

- 协调复杂性。

# 结论

该设置为实时数据流和处理提供了一个强大的解决方案，有效地集成了 Flink、Kafka 和 PostgreSQL。使用 Postgres 的主要目的是检查来自 IoT 设备的原始消息，而不是依赖于直接查询主题本身。它还展示了如何使用 JDBC 连接器进行数据输出，这可能是一个相对标准的做法。消息转换通过 DataStream API 完成。我希望进一步深入学习 SQL API，它提供了一个更加友好的接口。最后，关于如何管理数据流，建议根据具体需求选择单一作业或多个作业，确保可扩展性和可维护性。

# 下一步

1\. 使用 SQL API 进行数据转换。

2\. 根据作业复杂性优化资源使用。

3\. 探索 Flink 的高级功能以应对复杂的数据处理任务。

快乐流式处理！🚀

**敬请期待更多关于如何通过 Docker 集成和扩展数据工程解决方案的教程！**

*如有任何问题或建议，请随时在下方评论区与我们联系！*

# 准备好优化你的流数据应用了吗？

解锁数据的全部潜力，使用我们的[专家咨询服务](https://www.squadralabs.com)，为流数据应用量身定制。无论你是想增强实时分析、优化数据管道，还是提升性能，我们都能为你提供帮助。

# 参考资料

[https://www.confluent.io/blog/kafka-client-cannot-connect-to-broker-on-aws-on-docker-etc/](https://www.confluent.io/blog/kafka-client-cannot-connect-to-broker-on-aws-on-docker-etc/)

[https://mvnrepository.com/](https://mvnrepository.com/)

[https://nightlies.apache.org/flink/flink-docs-master/docs/connectors/datastream/jdbc/](https://nightlies.apache.org/flink/flink-docs-master/docs/connectors/datastream/jdbc/)

[https://nightlies.apache.org/flink/flink-docs-master/docs/deployment/overview/#session-mode](https://nightlies.apache.org/flink/flink-docs-master/docs/deployment/overview/#session-mode)

[https://nightlies.apache.org/flink/flink-docs-master/docs/deployment/resource-providers/standalone/docker/#using-flink-python-on-docker](https://nightlies.apache.org/flink/flink-docs-master/docs/deployment/resource-providers/standalone/docker/#using-flink-python-on-docker)

[https://medium.com/@sant1/flink-docker-kafka-faee9c0f1580](https://medium.com/@sant1/flink-docker-kafka-faee9c0f1580)
