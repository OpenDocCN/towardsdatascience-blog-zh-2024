# 使用 Python 和 Redpanda 聚合实时传感器数据

> 原文：[https://towardsdatascience.com/aggregating-real-time-sensor-data-with-python-and-redpanda-30a139d59702?source=collection_archive---------0-----------------------#2024-05-20](https://towardsdatascience.com/aggregating-real-time-sensor-data-with-python-and-redpanda-30a139d59702?source=collection_archive---------0-----------------------#2024-05-20)

## 使用 Python 和翻转窗口进行简单流处理

[](https://medium.com/@tomasatquix?source=post_page---byline--30a139d59702--------------------------------)[![Tomáš Neubauer](../Images/5eb14b73cfe100ef9a43148db6abd3a9.png)](https://medium.com/@tomasatquix?source=post_page---byline--30a139d59702--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--30a139d59702--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--30a139d59702--------------------------------) [Tomáš Neubauer](https://medium.com/@tomasatquix?source=post_page---byline--30a139d59702--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--30a139d59702--------------------------------) ·12 分钟阅读·2024年5月20日

--

![](../Images/d2873ac220cc6d905be3b31e86a270c8.png)

图片来源：作者

在本教程中，我想向你展示如何仅使用 Python（以及 Redpanda 作为消息代理）对传感器数据流进行降采样。目标是向你展示流处理的简单性，并证明你不需要一个高负载的流处理框架就能开始。

直到最近，流处理一直是一个复杂的任务，通常需要一定的 Java 专业知识。然而，随着 Python 流处理生态系统的逐渐成熟，Python 开发者现在有了更多的选择——例如 [Faust](https://faust-streaming.github.io/faust/introduction.html)、[Bytewax](https://bytewax.io/) 和 [Quix](https://quix.io/docs/quix-streams/quickstart.html)。稍后，我将提供更多背景，解释为什么这些库会与现有的 Java 为中心的选项竞争。

但首先让我们来处理当前的任务。我们将使用一个名为 Quix Streams 的 Python 库作为我们的流处理器。Quix Streams 与 Faust 非常相似，但它在语法上进行了优化，使其更加简洁，并使用了一个类似 Pandas 的 API，叫做 StreamingDataframes。

你可以使用以下命令安装 Quix Streams 库：

```py
pip install quixstreams
```

**你将构建的内容**

你将构建一个简单的应用程序，用来计算来自各种传感器的温度读数的滚动聚合。温度读数将以较高的频率进入，而这个应用程序将对读数进行聚合，并以较低的时间分辨率输出（每 10 秒一次）。你可以将其视为一种压缩形式，因为我们不希望在不必要的高分辨率数据上进行处理。

你可以在[这个GitHub仓库](https://github.com/quixio/template-windowing-reduce.git)中访问完整代码。

这个应用包含生成合成传感器数据的代码，但在实际场景中，这些数据可能来自多种传感器，例如安装在车队中的传感器或满载机器的仓库。

这里是基本架构的示意图：

![](../Images/c31694705376dcfea8141f0d9341bf62.png)

作者绘制的图

# 流处理管道的组件

上图展示了流处理管道的主要组件：传感器是**数据生产者**，Redpanda是**流数据平台**，Quix是**流处理器**。

**数据生产者**

这些是附加到生成数据的系统上的代码片段，例如ECU（发动机控制单元）上的固件、云平台的监控模块，或者记录用户活动的Web服务器。它们将原始数据以该平台能够理解的格式发送到流数据平台。

**流数据平台**

这是你存放流数据的地方。它在功能上与数据库处理静态数据类似。但与数据库的表不同，流数据使用主题（topics）。否则，它的特性与静态数据库相似。你需要管理谁可以消费和生成数据，数据应该遵循什么样的模式。不同于数据库的是，流数据是不断变化的，所以它并不设计为可以查询。你通常会使用流处理器来转换数据，并将其放到其他地方供数据科学家探索，或者将原始数据存入一个优化过的查询系统，如RisingWave或Apache Pinot，以便进行查询。然而，对于那些由流数据模式触发的自动化系统（如推荐引擎），这并不是理想的解决方案。在这种情况下，你肯定会使用专用的流处理器。

**流处理器**

这些是对数据执行持续操作的引擎，数据到达时会立即处理。它们可以比作处理任何应用程序后端数据的普通微服务，但有一个很大的不同。对于微服务来说，数据像雨滴一样一滴一滴地到达，每个“滴”都被独立处理。即使“下雨”很大，服务也能轻松跟上“滴”的速度而不至于溢出（可以类比为过滤系统，过滤掉水中的杂质）。

对于流处理器，数据以连续、广泛的水流形式到达。除非你改变设计，否则过滤系统会很快被压垮。也就是说，将数据流分开，并将较小的数据流路由到一系列过滤系统。这就是流处理器的工作原理。它们被设计为水平扩展并以并行的方式工作，像一个电池一样。而且它们从不停歇，持续处理数据，将过滤后的数据输出到流数据平台，后者充当流数据的蓄水池。为了让事情更复杂一些，流处理器通常需要跟踪先前接收到的数据，比如你将在这里尝试的窗口化示例。

请注意，还有“数据消费者”和“数据接收器”——这些系统消费处理后的数据（例如前端应用程序和移动应用）或将其存储用于离线分析（如数据仓库 Snowflake 或 AWS Redshift）。由于本教程不会涉及这些内容，我现在会跳过它们。

# 设置本地流数据集群

在本教程中，我将向你展示如何使用 Redpanda 的本地安装来管理流数据。我选择 Redpanda 是因为它非常容易在本地运行。

你将使用 Docker Compose 快速启动一个集群，其中包括 Redpanda 控制台，因此请确保你已先安装 Docker。

# 创建流式应用程序

首先，你将创建单独的文件来生成和处理你的流数据。这使得管理运行中的进程变得更容易。例如，你可以在不停止流处理器的情况下停止生产者。以下是你将创建的两个文件的概述：

+   **流数据生产者：** `sensor_stream_producer.py` 生成合成的温度数据，并将数据生产（即写入）到 Redpanda 中的“原始数据”源主题中。就像 Faust 示例一样，它以大约每5秒20次读取的分辨率生成数据，或者每秒大约4次读取。

+   **流处理器：** `sensor_stream_processor.py` 从“源”主题中消费（读取）原始温度数据，执行滚动窗口计算以降低数据的分辨率。它计算每10秒窗口中接收到数据的平均值，以便每10秒获得一次读取。然后，它将这些聚合后的读数生成到 Redpanda 中的 `agg-temperatures` 主题。

如你所见，流处理器完成了大部分繁重的工作，是本教程的核心。流数据生产者是一个用于模拟数据摄取过程的替代品。例如，在生产环境中，你可能会使用像这样的 [MQTT 连接器](https://github.com/quixio/quix-samples/tree/main/python/sources/MQTT) 从传感器获取数据并将其生产到主题中。

+   对于本教程，模拟数据更简单，因此让我们首先设置这些内容。

# 创建流数据生产者

你将从创建一个名为`sensor_stream_producer.py`的新文件并定义主要的 Quix 应用程序开始。（此示例已在 Python 3.10 上开发，但应该也可以在不同版本的 Python 3 上运行，只要你能够执行 `pip install quixstreams`）。

创建文件 `sensor_stream_producer.py` 并添加所有必要的依赖项（包括 Quix Streams）。

```py
from dataclasses import dataclass, asdict # used to define the data schema
from datetime import datetime # used to manage timestamps
from time import sleep # used to slow down the data generator
import uuid # used for message id creation
import json # used for serializing data

from quixstreams import Application
```

然后，定义一个 Quix 应用程序和目标主题来发送数据。

```py
 app = Application(broker_address='localhost:19092')

destination_topic = app.topic(name='raw-temp-data', value_serializer="json")
```

*value_serializer* 参数定义了预期源数据的格式（将被序列化为字节）。在这种情况下，你将发送 JSON。

让我们使用 dataclass 模块为温度数据定义一个非常基础的模式，并添加一个函数将其序列化为 JSON。

```py
@dataclass
class Temperature:
    ts: datetime
    value: int

    def to_json(self):
        # Convert the dataclass to a dictionary
        data = asdict(self)
        # Format the datetime object as a string
        data['ts'] = self.ts.isoformat()
        # Serialize the dictionary to a JSON string
        return json.dumps(data)
```

接下来，添加将负责将模拟温度传感器数据发送到我们的 Redpanda 源主题的代码。

```py
i = 0
with app.get_producer() as producer:
    while i < 10000:
        sensor_id = random.choice(["Sensor1", "Sensor2", "Sensor3", "Sensor4", "Sensor5"])
       temperature = Temperature(datetime.now(), random.randint(0, 100))
        value = temperature.to_json()

        print(f"Producing value {value}")
        serialized = destination_topic.serialize(
            key=sensor_id, value=value, headers={"uuid": str(uuid.uuid4())}
        )
        producer.produce(
            topic=destination_topic.name,
            headers=serialized.headers,
            key=serialized.key,
            value=serialized.value,
        )
        i += 1
        sleep(random.randint(0, 1000) / 1000)
```

这会生成 1000 条记录，记录之间的时间间隔是随机的，范围在 0 到 1 秒之间。它还会从 5 个选项中随机选择一个传感器名称。

现在，通过在命令行中运行以下命令来尝试生产者：

```py
python sensor_stream_producer.py
```

你应该能看到类似以下的日志数据输出到控制台：

```py
[data produced]
```

一旦确认它能正常工作，暂时停止进程（稍后你将在流处理过程中与其一起运行）。

# 创建流处理器

流处理器执行三项主要任务：1）从源主题消费原始温度读数，2）持续聚合数据，3）将聚合结果生成到汇聚主题。

让我们为这些任务的每个部分添加代码。在你的 IDE 中，创建一个名为 `sensor_stream_processor.py` 的新文件。

首先，像之前一样添加依赖项：

```py
import os
import random
import json
from datetime import datetime, timedelta
from dataclasses import dataclass
import logging
from quixstreams import Application

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)
```

让我们还设置一些我们的流处理应用程序所需的变量：

```py
TOPIC = "raw-temperature" # defines the input topic
SINK = "agg-temperature"  # defines the output topic
WINDOW = 10  # defines the length of the time window in seconds
WINDOW_EXPIRES = 1 # defines, in seconds, how late data can arrive before it is excluded from the window
```

我们稍后将更详细地解释窗口变量的含义，但现在让我们继续定义主要的 Quix 应用程序。

```py
app = Application(
    broker_address='localhost:19092',
    consumer_group="quix-stream-processor",
    auto_offset_reset="earliest",
)
```

请注意，这次有一些新的应用变量，分别是`consumer_group`和`auto_offset_reset`。要了解这些设置之间的相互作用，可以查看文章“[理解 Kafka 的自动偏移重置配置：使用案例和陷阱](https://quix.io/blog/kafka-auto-offset-reset-use-cases-and-pitfalls)”。

接下来，在核心流处理函数的两侧定义输入和输出主题，并添加一个函数，将传入的数据放入 DataFrame 中。

```py
input_topic = app.topic(TOPIC, value_deserializer="json")
output_topic = app.topic(SINK, value_serializer="json")

sdf = app.dataframe(input_topic)
sdf = sdf.update(lambda value: logger.info(f"Input value received: {value}"))
```

我们还添加了一行日志记录，以确保传入的数据是完整的。

接下来，添加一个自定义时间戳提取器，以便使用消息负载中的时间戳，而不是 Kafka 的时间戳。对于你的聚合操作，这基本上意味着你想使用生成读数的时间，而不是它被 Redpanda 接收的时间。或者更简单地说，“使用传感器的时间定义，而不是 Redpanda 的”。

```py
def custom_ts_extractor(value):

    # Extract the sensor's timestamp and convert to a datetime object
    dt_obj = datetime.strptime(value["ts"], "%Y-%m-%dT%H:%M:%S.%f") # 

    # Convert to milliseconds since the Unix epoch for efficent procesing with Quix
    milliseconds = int(dt_obj.timestamp() * 1000)
    value["timestamp"] = milliseconds
    logger.info(f"Value of new timestamp is: {value['timestamp']}")

    return value["timestamp"]

# Override the previously defined input_topic variable so that it uses the custom timestamp extractor 
input_topic = app.topic(TOPIC, timestamp_extractor=custom_ts_extractor, value_deserializer="json") 
```

我们为什么这么做呢？嗯，我们可以深入探讨在处理时使用哪种时间类型，但那是另一个话题。在自定义时间戳的情况下，我只是想说明，在流处理中有许多种时间解释方式，并且您不一定需要使用数据到达的时间。

接下来，在新窗口开始时初始化聚合的状态。当第一个记录到达窗口时，它将启动聚合。

```py
def initializer(value: dict) -> dict:

    value_dict = json.loads(value)
    return {
        'count': 1,
        'min': value_dict['value'],
        'max': value_dict['value'],
        'mean': value_dict['value'],
    }
```

这设置了窗口的初始值。对于最小值、最大值和均值，它们都是相同的，因为您只是将第一个传感器读数作为起点。

现在，让我们以“reducer”函数的形式添加聚合逻辑。

```py
def reducer(aggregated: dict, value: dict) -> dict:
    aggcount = aggregated['count'] + 1
    value_dict = json.loads(value)
    return {
        'count': aggcount,
        'min': min(aggregated['min'], value_dict['value']),
        'max': max(aggregated['max'], value_dict['value']),
        'mean': (aggregated['mean'] * aggregated['count'] + value_dict['value']) / (aggregated['count'] + 1)
    }
```

这个函数只有在对一个窗口执行多个聚合时才是必要的。在我们的例子中，我们为每个窗口创建计数、最小值、最大值和均值，因此我们需要预先定义这些值。

接下来是精彩的部分——添加滑动窗口功能：

```py
### Define the window parameters such as type and length
sdf = (
    # Define a tumbling window of 10 seconds
    sdf.tumbling_window(timedelta(seconds=WINDOW), grace_ms=timedelta(seconds=WINDOW_EXPIRES))

    # Create a "reduce" aggregation with "reducer" and "initializer" functions
    .reduce(reducer=reducer, initializer=initializer)

    # Emit results only for closed 10 second windows
    .final()
)

### Apply the window to the Streaming DataFrame and define the data points to include in the output
sdf = sdf.apply(
    lambda value: {
        "time": value["end"], # Use the window end time as the timestamp for message sent to the 'agg-temperature' topic
        "temperature": value["value"], # Send a dictionary of {count, min, max, mean} values for the temperature parameter
    }
)
```

这定义了 Streaming DataFrame，作为基于滑动窗口的一组聚合——在 10 秒钟不重叠的时间段内进行的一组聚合。

**提示**：如果您需要复习不同类型的窗口计算，请查看这篇文章：[“流处理中的窗口化指南](https://quix.io/blog/windowing-stream-processing-guide)”。

最后，将结果输出到下游的输出主题：

```py
sdf = sdf.to_topic(output_topic)
sdf = sdf.update(lambda value: logger.info(f"Produced value: {value}"))

if __name__ == "__main__":
    logger.info("Starting application")
    app.run(sdf)
```

**注意**：您可能会想知道，为什么生产者代码与用于发送合成温度数据的生产者代码看起来差异很大（即使用 `with app.get_producer() as producer()` 的部分）。这是因为 Quix 使用了不同的生产者函数来处理转换任务（即在输入和输出主题之间的任务）。

正如您在跟随过程中可能会注意到的，我们会反复修改 Streaming DataFrame（`sdf` 变量），直到它变成我们希望下游接收的最终形式。因此，`sdf.to_topic` 函数只是将 Streaming DataFrame 的最终状态逐行流式传输到输出主题。

`producer` 函数则用于从外部源获取数据，如 CSV 文件、MQTT 经纪人，或在我们的案例中，一个生成器函数。

# 运行流应用程序

最后，您可以运行我们的流应用程序，看看所有的组件是否协调工作。

首先，在终端窗口中重新启动生产者：

```py
python sensor_stream_producer.py
```

然后，在第二个终端窗口中，启动流处理器：

```py
python sensor_stream_processor.py
```

注意每个窗口中的日志输出，以确保一切运行顺利。

您还可以检查 Redpanda 控制台，以确保聚合数据正确地流式传输到目标主题（您可以在这里找到主题浏览器：[http://localhost:8080/topics](http://localhost:8080/topics)）。

![](../Images/46c639a04de0e311f01229b2061fdb4a.png)

作者截图

# 总结

你在这里尝试的只是流处理的一种方式。当然，还有像 Apache Flink 和 Apache Spark Streaming 这样的重型工具，它们也已在网上广泛介绍。但——这些主要是基于 Java 的工具。当然，你可以使用它们的 Python 封装，但当问题出现时，你还是会调试 Java 错误，而不是 Python 错误。而且，Java 技能在数据领域的从业者中并不普遍，尤其是随着数据工程师越来越多地与软件工程师共同调优流处理算法。

在本教程中，我们进行了一个简单的聚合作为我们的流处理算法，但实际上，这些算法通常会使用机器学习模型来转换数据——而机器学习的软件生态系统则被 Python 主导。

一个常被忽视的事实是，Python 是数据专家、机器学习工程师和软件工程师协作的通用语言。它甚至比 SQL 更好，因为你可以用它做一些与数据无关的事情，比如进行 API 调用和触发 Webhooks。这也是为什么像 Faust、Bytewax 和 Quix 这样的库不断发展的原因之一——它们旨在弥合这些不同学科之间的[阻抗差距](https://quix.io/blog/bridging-the-impedance-gap)。

希望我已经向你展示了，Python 是流处理的可行语言，而且 Python 在流处理领域的生态系统正在稳步成熟，能够与老旧的基于 Java 的生态系统一较高下。

+   提醒一下，本教程的所有代码都可以在[这个 GitHub 仓库](https://github.com/quixio/template-windowing-reduce)中找到。
