# 构建语义图书搜索：使用Apache Spark和AWS EMR Serverless扩展嵌入管道

> 原文：[https://towardsdatascience.com/building-a-semantic-book-search-part-2-scaling-the-embedding-pipeline-with-apache-spark-and-aws-1d074ee9cb55?source=collection_archive---------8-----------------------#2024-01-31](https://towardsdatascience.com/building-a-semantic-book-search-part-2-scaling-the-embedding-pipeline-with-apache-spark-and-aws-1d074ee9cb55?source=collection_archive---------8-----------------------#2024-01-31)

![](../Images/c41e10bbe243304d42ff3cdf276f15b0.png)

图片来源：Unsplash

## 使用OpenAI的Clip模型支持对70,000本书封面的自然语言搜索

[](https://medium.com/@erevear?source=post_page---byline--1d074ee9cb55--------------------------------)[![Eva Revear](../Images/675266fccb503690d50d83b8c92f48b8.png)](https://medium.com/@erevear?source=post_page---byline--1d074ee9cb55--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1d074ee9cb55--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1d074ee9cb55--------------------------------) [Eva Revear](https://medium.com/@erevear?source=post_page---byline--1d074ee9cb55--------------------------------)

·发布在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1d074ee9cb55--------------------------------) ·阅读时长8分钟·2024年1月31日

--

在[上一篇文章](https://medium.com/@erevear/building-semantic-book-search-with-openais-clip-model-b7c3dafa2bea)中，我做了一个小的PoC，看看能否使用OpenAI的Clip模型来构建语义图书搜索。依我看，效果出乎意料地好，但我不禁想，是否有更多数据会更好。之前的版本仅使用了大约3.5千本书，但在[Openlibrary数据集](https://openlibrary.org/data)中有数百万本书，我觉得尝试添加更多的搜索选项是值得的。

然而，完整的数据集大约有40GB，试图在我的小笔记本电脑上，甚至在Colab笔记本中处理这么多数据有点困难，所以我不得不想办法构建一个管道来处理过滤和嵌入更大的数据集。

TLDR; 它改善了搜索吗？我认为是的！我们将数据量增加了15倍，这为搜索提供了更多可用数据。它还不完美，但我觉得结果相当有趣；虽然我没有做正式的准确度评测。

这是一个无论如何表述都无法在上一版本中运行的例子，但在使用更多数据的版本中效果相当不错。

![](../Images/607d576fe9f3cd3c251f1adee292b71f.png)

作者图片

如果你感兴趣，可以在[Colab](https://colab.research.google.com/drive/1QH_ZlDKAJ9I5yEitew6X_ZJWIvcmsqfv?usp=sharing)上试试看！

总体来说，这是一次有趣的技术旅程，过程中遇到了许多障碍和学习机会。技术栈仍然包括OpenAI的Clip模型，但这次我使用了Apache Spark和AWS EMR来运行嵌入管道。

# **技术栈**

![](../Images/23511f9399b09dea8df6e74c8f6e841d.png)

图片由作者提供

这似乎是一个使用Spark的好机会，因为它允许我们将嵌入计算并行化。

我决定在EMR Serverless中运行管道，这是AWS提供的一项相对较新的服务，提供了一个无服务器环境来运行EMR并自动管理资源的扩展。我认为这对于这个用例很合适——而不是在EC2集群上启动EMR——因为这是一个相对临时的项目，我对集群成本很敏感，而且最初我不确定这个作业需要什么资源。EMR Serverless使得实验作业参数变得非常简单。

以下是我完成一切并使其运行的完整过程。我想可能有更好的方法来管理某些步骤，这只是对我有效的方式，如果你有想法或意见，请一定分享！

## 使用Spark构建嵌入管道作业

初步步骤是编写Spark作业。整个管道分为两个阶段，第一个阶段获取初始数据集并筛选出近十年内的小说（过去10年）。这导致了大约25万本书籍，其中约7万本有封面图片可以下载并嵌入到第二阶段中。

首先，我们从原始数据文件中提取相关列。

然后对数据类型进行一些通用的数据转换，并筛选出除英文小说（超过100页）以外的所有数据。

第二阶段获取第一阶段的输出数据集，并通过从Hugging Face下载的Clip模型处理图片。这里的关键步骤是将我们需要应用于数据的各种函数转化为Spark UDF。最关键的函数是get_image_embedding，它接受图片并返回嵌入信息。

我们将其注册为UDF：

然后在数据集上调用UDF：

## 设置向量数据库

作为代码中的最后一个可选步骤，我们可以设置一个向量数据库，在这个案例中是Milvus，用于加载和查询。请注意，我没有在此项目的云作业中执行此操作，因为我将我的嵌入数据序列化存储，以便不需要保持集群长时间运行。然而，设置Milvus并将Spark DataFrame加载到集合中是相当简单的。

首先，创建一个集合，并在图像嵌入列上创建索引，数据库可以用来进行搜索。

然后我们可以在Spark脚本中访问该集合，并从最终的DataFrame中将嵌入加载到其中。

最后，我们可以使用与上述UDF相同的方法将搜索文本嵌入，并使用嵌入信息查询数据库。数据库负责进行繁重的匹配工作，找出最佳匹配。

## 在AWS中设置管道

**前提条件**

现在有一些设置需要完成，以便在 EMR Serverless 上运行这些作业。

我们需要以下先决条件：

+   一个 S3 桶，用于存放作业脚本、输入和输出以及作业所需的其他工件

+   一个具有 S3 读取、列出和写入权限的 IAM 角色，以及 Glue 的读取和写入权限。

+   一个信任策略，允许 EMR 作业访问其他 AWS 服务。

AWS 文档中有关于角色和权限策略的详细描述，以及如何开始使用 EMR Serverless 的概述：[开始使用 Amazon EMR Serverless](https://docs.aws.amazon.com/emr/latest/EMR-Serverless-UserGuide/getting-started.html)

接下来，我们需要设置一个 EMR Studio：[创建 EMR Studio](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-studio-create-studio.html)

**通过互联网网关访问网页**

另一个特定于此作业的设置是，我们必须允许该作业访问互联网，而默认情况下 EMR 应用程序是无法做到这一点的。正如我们在脚本中看到的，作业需要访问要嵌入的图像，以及访问 Hugging Face 以下载模型配置和权重。

注意：可能有更高效的方式来处理模型，而不是将其下载到每个工作节点（例如广播、将其存储在系统中的某个地方等），但在这种情况下，对于一次数据运行，这就足够了。

无论如何，允许 Spark 作业运行的机器访问互联网需要配置具有 NAT 网关的私有子网的 VPC。所有这些设置都从访问 AWS VPC 界面开始 -> 创建 VPC -> 选择 VPC 和更多选项 -> 选择至少一个 NAT 网关选项 -> 点击创建 VPC。

![](../Images/c949e2d8bba19e439972c50c44e0575e.png)

图像由作者提供

VPC 设置需要几分钟。一旦完成，我们还需要在安全组界面中创建一个安全组，并将刚才创建的 VPC 附加到它。

**创建 EMR Serverless 应用程序**

现在，来看看将提交作业的 EMR Serverless 应用程序！创建并启动一个 EMR Studio 应该会打开一个界面，提供几个选项，包括创建应用程序。在创建应用程序的 UI 中，选择使用自定义设置 -> 网络设置。在这里，VPC、两个私有子网和安全组将发挥作用。

![](../Images/ecdc638917b82e45aa67258591d2a561.png)

图像由作者提供

**构建虚拟环境**

最后，环境中没有太多库，因此为了添加额外的 Python 依赖项，我们可以使用本地 Python 或创建并打包一个虚拟环境：[在 EMR Serverless 中使用 Python 库](https://docs.aws.amazon.com/emr/latest/EMR-Serverless-UserGuide/using-python-libraries.html)。

我选择了第二种方式，最简单的方法是使用 Docker，因为它允许我们在运行 EMR 作业的 Amazon Linux 发行版中构建虚拟环境（在其他发行版或操作系统中做这件事可能会变得非常混乱）。

另一个警告：小心选择与您使用的 Python 版本对应的 EMR 版本，并相应地选择合适的包版本。

Docker 过程将打包好的虚拟环境输出为 pyspark_dependencies.tar.gz，然后将其与作业脚本一起上传到 S3 存储桶。

然后，我们可以将这个打包好的环境和其他 Spark 作业配置一起发送出去。

太好了！我们有了作业脚本、环境依赖项、网关和一个 EMR 应用程序，我们可以提交作业了！不过，别着急！接下来才是真正有趣的部分：Spark 调优。

如前所述，EMR Serverless 会自动扩展以处理我们的工作负载，这通常是很好的，但我发现（事后看来是显而易见的）它对于这个特定的用例并没有什么帮助。

几万条记录根本不算是“大数据”；Spark 需要处理的是 TB 级别的数据，而我发送的基本上只是几千个图片 URL（甚至连图片本身都没有）。如果让 EMR Serverless 自行决定，它会把任务发送到一个节点，在单线程中处理，完全违背了并行化的初衷。

此外，虽然嵌入作业处理的数据量相对较小，但它们会显著扩大数据量，因为嵌入体积相当大（在 Clip 的情况下是 512）。即使你让那个节点不停地运行几天，它也会在完成所有数据处理之前就耗尽内存。

为了让它运行，我尝试了几个 Spark 属性，目的是能够在集群中使用大型机器，但将数据拆分成非常小的分区，以便每个核心只需要处理少量数据并输出：

+   spark.executor.memory：每个执行器进程使用的内存量。

+   spark.sql.files.maxPartitionBytes：读取文件时单个分区中要打包的最大字节数。

+   spark.executor.cores：每个执行器使用的核心数。

你需要根据数据的具体性质调整这些设置，嵌入仍然不是一个快速的过程，但它能够处理我的数据。

## **结论**

与我的[上一篇文章](https://medium.com/@erevear/building-semantic-book-search-with-openais-clip-model-b7c3dafa2bea)一样，结果当然不是完美的，绝对不能替代其他人提供的真实书籍推荐！但话说回来，对于我搜索的一些问题，确实有一些准确的答案，我觉得还挺酷的。

如果你想自己玩一下这个应用，它在[Colab](https://colab.research.google.com/drive/1QH_ZlDKAJ9I5yEitew6X_ZJWIvcmsqfv?usp=sharing)上，整个管道的代码在[Github](https://github.com/erevear/book_semantic_search_cloud)上！
