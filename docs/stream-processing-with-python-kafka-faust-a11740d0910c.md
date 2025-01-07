# 使用 Python、Kafka 和 Faust 进行流处理

> 原文：[`towardsdatascience.com/stream-processing-with-python-kafka-faust-a11740d0910c?source=collection_archive---------2-----------------------#2024-02-18`](https://towardsdatascience.com/stream-processing-with-python-kafka-faust-a11740d0910c?source=collection_archive---------2-----------------------#2024-02-18)

## 如何在高吞吐量时间序列数据上进行流处理并应用实时预测模型

[](https://medium.com/@aliosia?source=post_page---byline--a11740d0910c--------------------------------)![Ali Osia](https://medium.com/@aliosia?source=post_page---byline--a11740d0910c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a11740d0910c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a11740d0910c--------------------------------) [Ali Osia](https://medium.com/@aliosia?source=post_page---byline--a11740d0910c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a11740d0910c--------------------------------) ·阅读时长：7 分钟·2024 年 2 月 18 日

--

![](img/ef4f06938ee58f847fdbc6bc1a65aaf2.png)

图片来自[JJ Ying](https://unsplash.com/@jjying?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

大多数流处理库并不适合 Python，而大多数机器学习和数据挖掘库却是基于 Python 的。尽管[Faust](https://faust-streaming.github.io/faust/introduction.html)库旨在将 Kafka 流处理理念引入 Python 生态系统，但在易用性方面可能会带来挑战。本文件作为一个教程，提供了有效使用 Faust 的最佳实践。

在第一部分，我介绍了流处理概念的基本概述，广泛借鉴了《*设计数据密集型应用*》一书[1]。随后，我探讨了 Faust 库的关键功能，特别是 Faust 窗口，这部分通常在现有文档中难以理解且难以高效利用。因此，我提出了一种通过利用库自身函数的替代方法来使用 Faust 窗口。最后，我分享了在 Google Cloud Platform 上实现类似管道的经验。

# 流处理

*流*（stream）指的是随着时间推移逐步可用的无界数据。*事件*（event）是一个小型的、独立的对象，包含某一时刻发生的事情的详细信息，例如用户交互。事件由*生产者*（producer）生成（例如温度传感器），并可能被一些*消费者*（consumers）消费（例如在线仪表盘）。传统的数据库不适合存储高吞吐量的事件流。这是因为消费者需要定期轮询数据库以识别新事件，从而产生显著的开销。相反，最好是让消费者在新事件出现时收到通知，而*消息系统*（messaging systems）正是为此而设计的。

*消息代理*（message broker）是一种广泛采用的消息传递系统，其中生产者将消息写入代理，消费者由代理通知并接收这些消息。基于*AMQP 的消息代理*（AMQP-based message brokers），如*RabbitMQ*，常用于服务之间的异步消息传递和任务队列。与数据库不同，它们采用瞬时消息的理念，仅在消息被消费者确认后才会删除消息。当消息处理变得资源密集时，可以通过使用多个消费者以负载均衡的方式从相同主题读取来实现并行处理。在这种方法中，消息会被随机分配给消费者进行处理，这可能导致处理的顺序与接收的顺序不同。

另一方面，*基于日志的消息代理*（log-based message brokers），如*Apache Kafka*，将数据库存储的持久性与消息系统的低延迟通知能力结合在一起。它们利用分区日志结构，其中每个分区表示按追加顺序存储在磁盘上的记录序列。这一设计使得重新读取旧消息成为可能。Kafka 中的负载均衡是通过将每个消费者分配给一个分区来实现的，从而消息处理的顺序与接收的顺序一致，但消费者的数量受限于可用分区的数量。

*流处理*（stream processing）涉及对流执行操作，如处理流并生成新的流、将事件数据存储在数据库中或在仪表盘上可视化数据。*流分析*（stream analytics）是一个常见的使用案例，其中我们在定义的时间窗口内聚合来自一系列事件的信息。*滚动窗口*（Tumbling windows，非重叠）和*跳动窗口*（Hopping windows，重叠）是流分析中常用的窗口类型。流分析使用案例的例子可以是简单地计算过去一小时内的事件数，或者对事件应用复杂的时间序列预测模型。

流分析面临着区分事件创建时间*(事件时间)*和事件*处理时间*的挑战，因为事件处理可能由于排队或网络问题引入延迟。基于处理时间定义窗口是一种更简单的方法，特别是当处理延迟较小时。然而，基于事件时间定义窗口则更具挑战性。这是因为无法确定窗口内的所有数据是否已经接收完毕，或者是否还有未处理的事件。因此，需要处理在窗口被认为已完成后仍然到达的*滞后事件*。

在涉及复杂流分析的应用中，如时间序列预测，通常需要将一系列有序的消息作为一个整体在窗口内进行处理。在这种情况下，消息之间存在强烈的相互依赖关系，导致很难从代理中确认并移除单个消息。因此，基于日志的消息代理成为了一种更可取的选择。此外，在这种情况下，由于窗口中的所有消息需要一起考虑，平行处理可能不可行或实现过于复杂。然而，应用复杂的机器学习模型来处理数据可能需要大量计算资源，因此必须采取替代的平行处理方法。本文旨在提出一种解决方案，以在高吞吐量流处理应用中有效地使用资源密集型机器学习模型。

# Faust 流处理

有多个流处理库可供选择，例如 Apache Kafka Streams、Flink、Samza、Storm 和 Spark Streaming。每个库都有自己的优缺点，但其中许多并不是特别适合 Python。不过，*Faust*是一个基于 Python 的流处理库，使用 Kafka 作为底层消息系统，旨在将 Kafka Streams 的理念引入 Python 生态系统。不幸的是，Faust 的文档可能会让人困惑，源代码也可能难以理解。例如，理解 Faust 中窗口的工作方式在不参考复杂的源代码的情况下是具有挑战性的。此外，[Faust](https://github.com/robinhood/faust)（v1）和[Faust-Streaming](https://github.com/faust-streaming/faust)（v2）仓库中存在许多开放问题，解决这些问题并非一件简单的事情。接下来，将提供有关 Faust 底层结构的必要知识，并附上代码片段，帮助有效利用 Faust 库。

使用 Faust 的第一步是创建一个*应用*并配置项目，通过指定代理和其他必要的参数。一个有用的参数是`table_cleanup_interval`，将在后续讨论。

```py
app = faust.App(
    app_name, 
    broker=broker_address, 
    store=rocksdb_address, 
    table_cleanup_interval=table_cleanup_interval
)
```

然后，你可以使用*agent*装饰器定义一个流处理器，从 Kafka 主题中消费数据，并对每个接收到的事件执行某些操作。

```py
schema = faust.Schema(value_serializer='json')
topic = app.topic(topic_name, schema=schema)

@app.agent(topic)
async def processor(stream):
    async for event in stream:
        print(event) 
```

为了在流处理器中保持状态，我们可以使用 Faust 的*Table*。表是一个分布式的内存字典，由 Kafka 变更日志主题支持。你可以将`table`视为一个可以在流处理器中设置的 Python 字典。

```py
table = app.Table(table_name, default=int)

@app.agent(topic)
async def processor(stream):
    async for event in stream:
        table[key] += event 
```

## Faust 窗口

让我们考虑一个时间序列问题，每秒我们需要从前 10 秒钟的样本中进行预测。因此，我们需要 10 秒重叠的窗口，重叠时间为 1 秒。为了实现这个功能，我们可以利用 Faust 的[*windowed tables*](https://faust-streaming.github.io/faust/userguide/tables.html#windowing)，但在 Faust 文档中对它们的解释不够充分，常常导致困惑。

理想情况下，流处理库应该自动执行以下任务：

1.  为每个窗口保持状态（事件列表）；

1.  确定新事件的相关窗口（最后 10 个窗口）；

1.  更新这些窗口的状态（将新事件附加到它们各自列表的末尾）；

1.  在窗口关闭时应用一个函数，使用窗口的状态作为输入。

在下面的代码片段中，你可以观察到 Faust 文档中建议的构建窗口并在流处理器中使用它的方法（参考 Faust 库中的[这个](https://github.com/faust-streaming/faust/blob/master/examples/windowed_aggregation.py)示例）：

```py
# Based on Fuast example
# Do not use this

window_wrapper = app.Table(
    table_name, default=list, on_window_close=window_close
).hopping(
    10, 1, expires=expire_time
)

@app.agent(topic)
async def processor(stream):
    async for event in stream:
        window_set = window_wrapper[key]
        prev = window_set.value()
        prev.append(event)
        window_wrapper[key] = prev
```

在提供的代码中，`window_wrapper`对象是[*WindowWrapper*](https://github.com/faust-streaming/faust/blob/ebf66ae031c3eb462ade320c73e84d1c4cb7a32f/faust/tables/wrappers.py#L312)类的一个实例，提供了一些所需的功能。`expires`参数决定了窗口生命周期的持续时间，从创建开始计算。一旦这个指定的时间过去，窗口就被视为关闭。Faust 会定期检查`table_cleanup_interval`持续时间，以识别已关闭的窗口。然后，它会应用`window_close`函数，使用窗口状态作为输入。

当你调用`window_wrapper[key]`时，它返回一个类型为*WindowSet*的对象，该对象内部包含所有相关的窗口。通过调用`window_set.value()`，你可以访问最新窗口的状态，另外通过调用`window_set.delta(30)`，你可以访问 30 秒前的窗口状态。此外，你还可以通过为`window_wrapper[key]`赋新值来更新*最新*窗口的状态。这种方法适用于滚动窗口，但不适用于跳跃窗口，跳跃窗口需要更新多个窗口的状态。

> [Faust 文档：] 此时，在访问跳跃表中的数据时，我们总是访问给定时间戳的最新窗口，而且我们无法修改这种行为。

虽然 Faust 支持维护窗口状态、识别相关窗口并在已关闭的窗口上应用函数，但它并没有完全解决第三个功能，即更新所有相关窗口的状态。

# Google Cloud 解决方案

我想简要讨论一下我在使用 Google Cloud Platform（GCP）时的负面体验。GCP 推荐使用 Google Pub/Sub 作为消息代理，Apache Beam 作为流处理库，Google Dataflow 作为执行工具，Google BigQuery 作为数据库。然而，当我尝试使用这个技术栈时，我遇到了许多问题，导致使用起来非常具有挑战性。

在 Python 中使用 Google Pub/Sub 证明是比较慢的（可以查看[这个](https://medium.com/google-cloud/how-long-does-google-dataflow-pick-and-process-pub-sub-messages-in-real-time-8ac19da774a2)和[这个](https://cloud.google.com/blog/products/data-analytics/testing-cloud-pubsub-clients-to-maximize-streaming-performance)），这让我放弃了它，转而使用 Kafka。Apache Beam 是一个文档齐全的库，但与 Kafka 一起使用时却遇到了自己的一些问题。直接运行器有漏洞，需要使用 Dataflow，且由于等待机器配置，导致了显著的时间延迟。此外，我还遇到了窗口触发延迟的问题，尽管我尝试过解决这个问题但都没有成功（可以查看这个[GitHub 问题](https://github.com/apache/beam/issues/27238)和这个[Stack Overflow 贴文](https://stackoverflow.com/questions/76545125/google-dataflow-has-delay-in-stream-jobs-using-apache-beam-and-kafka)）。而且，由于多个组件的复杂集成，调试整个系统是一个巨大的挑战，这让我对日志的控制非常有限，也使得很难 pinpoint（定位）Pub/Sub、Beam、Dataflow 或 BigQuery 中问题的根本原因。总的来说，我在使用 Google Cloud Platform 的过程中，遇到了 Python 中 Google Pub/Sub 性能慢、使用 Apache Beam 与 Kafka 时的 bugs 以及调试这些互联系统的整体困难。

[1] Kleppmann, Martin. *设计数据密集型应用：可靠、可扩展和可维护系统背后的核心理念*。 “ O’Reilly Media, Inc.”, 2017。
