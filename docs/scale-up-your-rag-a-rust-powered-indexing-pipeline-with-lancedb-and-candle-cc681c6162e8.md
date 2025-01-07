# 扩展你的RAG：基于Rust的LanceDB和Candle索引管道

> 原文：[https://towardsdatascience.com/scale-up-your-rag-a-rust-powered-indexing-pipeline-with-lancedb-and-candle-cc681c6162e8?source=collection_archive---------2-----------------------#2024-07-11](https://towardsdatascience.com/scale-up-your-rag-a-rust-powered-indexing-pipeline-with-lancedb-and-candle-cc681c6162e8?source=collection_archive---------2-----------------------#2024-07-11)

## 构建大规模文档处理的高性能嵌入和索引系统

[](https://medium.com/@alon.agmon?source=post_page---byline--cc681c6162e8--------------------------------)[![Alon Agmon](../Images/c64e33ca886da4f4984c96acc7116a4d.png)](https://medium.com/@alon.agmon?source=post_page---byline--cc681c6162e8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cc681c6162e8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cc681c6162e8--------------------------------) [Alon Agmon](https://medium.com/@alon.agmon?source=post_page---byline--cc681c6162e8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cc681c6162e8--------------------------------) ·12分钟阅读·2024年7月11日

--

![](../Images/96228249c0ebdc4684b0beec98e23e54.png)

图片来源：[Marc Sendra Martorell](https://unsplash.com/@marcsm?utm_source=medium&utm_medium=referral) 来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 1\. 简介

最近，基于检索增强生成（RAG）的AI应用已成为构建生成型AI应用程序的*事实上的*标准，尤其是在使用大型语言模型时。RAG通过确保生成模型使用适当的上下文来增强文本生成，同时避免了对LLM进行微调所涉及的时间、成本和复杂性。RAG还可以更高效地使用外部数据源，并更容易地更新模型的“知识”。

尽管基于RAG的AI应用通常可以使用更小型或更简单的LLM，但它们仍然依赖于一个强大的流程来嵌入和索引所需的知识库，并且需要能够高效地检索并将相关上下文注入到模型提示中。

在许多应用场景中，RAG可以通过使用任何一个广泛可用的优秀框架，在几行代码中实现。本文侧重于更复杂且要求更高的流程，例如当需要嵌入和索引的数据量较大，或者需要非常频繁或极快地更新时。

本文展示了如何设计一个 Rust 应用程序，能够以惊人的速度读取、分块、嵌入并将文本文档存储为向量。利用 Hugging Face 的 Candle 框架和 LanceDB，它展示了如何开发一个端到端的 RAG 索引管道，可以作为独立应用程序部署到任何地方，并作为强大管道的基础，即使在非常苛刻和孤立的环境中也是如此。

本文的主要目的是创建一个可以应用于现实世界用例的工作示例，同时引导读者了解其关键设计原则和构建模块。该应用程序及其源代码可在随附的 GitHub 仓库中获得（链接见下），可以直接使用或作为进一步开发的示例。

本文的结构如下：第 2 节高层次地解释了主要的设计选择和相关组件。第 3 节详细介绍了管道的主要流程和组件设计。第 4 节和第 5 节分别讨论了嵌入流程和写入任务。第 6 节作结。

# 2. 设计选择与关键组件

我们的主要设计目标是构建一个独立的应用程序，能够在没有外部服务或服务器进程的情况下运行端到端的索引管道。其输出将是一个数据文件集，采用 LanceDB 的[Lance 格式](https://lancedb.github.io/lance/)，这些文件可以被像 LangChain 或 Llamaindex 这样的框架使用，并且可以通过 DuckDB 或任何使用 LanceDB API 的应用程序进行查询。

该应用程序将用 Rust 编写，并基于两个主要的开源框架：我们将使用**Candle ML**框架处理生成文档嵌入的机器学习任务，采用类似 BERT 的模型，并使用**LanceDB**作为我们的向量数据库和检索 API。

![](../Images/8fc3ab1d758e41063ba4ab6113b0840a.png)

处理文档索引管道所有阶段的 Rust 应用程序（图片由作者提供）

在深入讲解应用程序的细节和结构之前，简要介绍一下这些组件和设计选择可能会有所帮助。

Rust 是性能至关重要时的显而易见选择。尽管 Rust 的学习曲线较陡，但其性能与本地编程语言（如 C 或 C++）相当，而且提供了丰富的抽象和扩展库，使得内存安全性和并发等挑战比本地语言更容易处理。结合 Hugging Face 的 Candle 框架，在本地 Rust 中使用 LLM 和嵌入模型变得前所未有的顺畅。

然而，LanceDB是RAG堆栈中的一个相对较新的成员。它是一个精简型的嵌入式向量数据库（类似于SQLite），可以直接集成到应用程序中，而无需单独的服务器进程。因此，它可以部署在任何地方并嵌入到任何应用中，同时提供极快的搜索和检索能力，即使是在远程对象存储中的数据上，例如AWS S3。正如之前提到的，它还提供与LangChain和LlamaIndex的集成，并且可以使用DuckDB进行查询，这使得它成为向量存储的一个更具吸引力的选择。

在我在一台10核Mac（没有GPU加速）上进行的简单测试中，应用程序在不到一秒的时间里处理、嵌入并存储了大约25,000个词（相当于17个文本文件，每个文件包含大约1,500个词）。这一令人印象深刻的吞吐量展示了Rust在处理CPU密集型任务和I/O操作方面的高效性，以及LanceDB强大的存储能力。两者结合在一起，对于解决大规模数据嵌入和索引挑战表现出色。

![](../Images/038d06947393f658500ad6d318aa8974.png)

图片来源：[Tharoushan Kandarajah](https://unsplash.com/@tharoushan?utm_source=medium&utm_medium=referral) 在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)上的作品

# 3\. 流水线架构与流程

我们的RAG应用程序和索引流水线包含两个主要任务：一个*读取和嵌入任务*，它从文本文件中读取文本并使用嵌入模型将其嵌入到BERT向量中，另一个是*写入任务*，它将嵌入写入向量存储。由于前者大多由CPU限制（嵌入单个文档可能需要多个机器学习模型操作），而后者大多是在等待I/O，因此我们将这两个任务分配到不同的线程中。此外，为了避免争用和背压，我们还将通过一个[多生产者单消费者通道](https://doc.rust-lang.org/std/sync/mpsc/index.html)连接这两个任务。在Rust（以及其他语言）中，同步通道基本上可以实现线程安全和异步的线程间通信，从而使其能够更好地扩展。

主要流程很简单：每当一个嵌入任务完成将文本文档嵌入向量后，它会将向量及其ID（文件名）“发送”到通道，并立即继续处理下一个文档（见下图中的读取端）。与此同时，写入任务会不断地从通道中读取数据，将向量分块存储在内存中，并在达到一定大小时刷新数据。因为我预计嵌入任务会更加耗时和资源，所以我们会将其并行化，利用运行应用程序的机器上可用的多个核心。换句话说，我们将有*多个*嵌入任务来读取和嵌入文档，以及一个单独的写入任务来分块并将向量写入数据库。

![](../Images/50e34f48e49edcc6c561c287312198d8.png)

流水线设计与应用流程（图片由作者提供）

让我们从`main()`函数开始，这将使得管道的流程更加清晰。

如上所示，在设置好通道（第3行）后，我们初始化了写入任务线程，该线程开始从通道中轮询消息，直到通道关闭。接着，它列出了相关目录中的文件并将它们存储在一个字符串集合中。最后，它使用*Rayon*通过`par_iter`函数并行处理文件列表，以便使用`process_text_file()`函数对其进行并行化处理。使用Rayon将允许我们根据机器的能力尽可能扩展文档的并行处理。

如你所见，流程相对简单，主要协调两个主要任务：文档处理和向量存储。这个设计允许高效的并行化和可扩展性。文档处理任务使用Rayon来并行化文件处理，最大化利用可用的系统资源。同时，存储任务负责高效地将嵌入的向量写入LanceDB。关注点的分离不仅简化了整体架构，还允许对每个任务进行独立优化。在接下来的章节中，我们将更详细地探讨这两个函数。

# 4\. 使用Candle进行文档嵌入

正如我们之前所看到的，在我们的管道的一端，我们有多个嵌入任务，每个任务都在自己的线程上运行。Rayon的`iter_par`函数有效地遍历文件列表，在每个文件上调用`process_text_file()`函数，同时最大化并行化。

让我们从函数本身开始：

该函数首先获取对嵌入模型的引用（这是函数中最棘手的部分，我稍后会详细讲解）。接下来，它将文件分成一定大小的块，并对每个块调用嵌入函数（该函数实际上调用的是模型本身）。嵌入函数返回一个类型为`Vec<f32>`的向量（大小为[1, 384]），这是嵌入和归一化每个块的结果，之后计算所有文本块的平均值。当这一部分完成后，向量连同文件名一起发送到通道，用于持久化、查询和由写入任务进行检索。

如你所见，绝大部分工作由`BertModelWrapper`结构体完成（我们在第2行获取了该结构体的引用）。`BertModelWrapper`的主要目的是封装模型的加载和嵌入操作，并提供`embed_sentences()`函数，该函数本质上将一组文本块嵌入并计算它们的平均值，生成一个单一的向量。

为了实现这一点，`BertModelWrapper` 使用了 HuggingFace 的 Candle 框架。Candle 是一个本地的 Rust 库，其 API 类似于 PyTorch，用于加载和管理 ML 模型，并且对在 HuggingFace 上托管的模型提供了非常便捷的支持。虽然 Rust 中还有其他生成文本嵌入的方式，但 Candle 在本地化和不依赖其他库方面似乎是“最干净”的选择。

尽管对包装器代码的详细解释超出了我们当前的范围，但我在另外一篇文章中有更详细的说明（[链接在此](https://medium.com/towards-data-science/streamlining-serverless-ml-inference-unleashing-candle-frameworks-power-in-rust-c6775d558545)），其源代码可以在附带的 GitHub 仓库中找到。你也可以在 Candle 的示例仓库中找到很好的示例。

然而，有一个重要的部分需要解释，这就是我们在使用嵌入模型的方式，因为这将在任何需要在流程中使用大规模模型的地方都将是一个挑战。简而言之，我们希望多个线程能够同时使用我们的模型来执行嵌入任务，但由于加载时间的问题，我们不希望每次需要模型时都重新创建它。换句话说，我们希望确保每个线程只创建一个模型实例，该实例由线程拥有并在多个嵌入任务中重复使用。

由于 Rust 的众所周知的限制，这些要求并不是非常容易实现。如果你不想深入了解如何在 Rust 中实现这一部分，可以跳过此部分（直接使用代码）。

我们从获取模型引用的函数开始：

我们的模型被封装在几个层次中，以实现上述功能。首先，它被封装在 `thread_local` 子句中，这意味着每个线程将有自己的惰性副本——即所有线程都可以访问 `BERT_MODEL`，但在第一次调用 `with()`（第18行）时触发的初始化代码将仅在每个线程中惰性执行一次，这样每个线程就会拥有一个有效的引用，该引用只会初始化一次。第二层是引用计数类型——`Rc`，它简化了创建模型引用的过程，而无需处理生命周期。每次我们在其上调用 `clone()` 时，我们都会得到一个引用，该引用在超出作用域时会自动释放。

最后一层实际上是服务函数 `get_model_reference()`，它简单地调用了 `with()` 函数，从而提供了访问线程本地内存区域的权限，该区域保存已初始化的模型。对 `clone()` 的调用将为我们提供模型的线程本地引用，如果模型尚未初始化，则初始化代码将首先执行。

现在我们已经了解了如何运行多个并行执行的嵌入任务，并将向量写入通道，我们可以继续处理管道的另一部分——写入任务。

![](../Images/cd944fb01c76ea59b2cda960c7e703a1.png)

图片来源：[SpaceX](https://unsplash.com/@spacex?utm_source=medium&utm_medium=referral) 通过 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 4\. 写入任务：高效的向量存储

写入任务相对简单，主要作为一个接口，封装了 LanceDB 的写入功能。回想一下，LanceDB 是一个嵌入式数据库，这意味着它作为一个库的查询引擎，读取和写入可以存储在远程存储上的数据，例如 AWS S3，并且它不拥有数据。这使得它在需要处理大规模数据且低延迟的用例中尤其方便，而无需管理单独的数据库服务器。

LanceDB 的 Rust API 使用 Arrow 来定义 schema 和表示数据（其 Python API 对某些人来说可能更方便）。例如，以下是我们如何在 Arrow 格式中定义 schema：

如你所见，我们当前的 schema 包含两个字段：“filename”字段，它将保存实际的文件位置并作为我们的键，以及“vector”字段，它保存实际的文档向量。在 LanceDB 中，向量使用 `FixedSizeList` Arrow 类型表示（表示一个数组），而向量中的每个项目将是 Float32 类型（向量的长度，最后设置，将是 384）。

连接到 LanceDB 非常简单，只需要一个存储位置，可以是本地存储路径或 S3 URI。然而，使用 Rust 和 Arrow 数据结构将数据附加到 LanceDB 上并不十分开发者友好。与其他基于 Arrow 的列式数据结构类似，插入数据时，不是附加一行行的数据列表，而是每一列都表示为一个值的列表。例如，如果你有 10 行数据需要插入且有 2 列，你需要附加 2 个列表，每个列表包含 10 个值。

下面是一个例子：

代码的核心在第 2 行，我们从我们的 schema 和列数据构建一个 Arrow 的 `RecordBatch`。在这个例子中，我们有两列——文件名和向量。我们使用两个列表初始化我们的记录批次：`key_array`，一个包含文件名的字符串列表，以及 `vectors_array`，一个包含向量数组的列表。从这里开始，Rust 的严格类型安全要求我们在将数据传递给第 1 行获得的表引用的 `add()` 函数之前，必须对数据进行大量的包装。

为了简化这一逻辑，我们创建了一个存储模块，封装了这些操作，并提供一个基于 `connect(uri)` 函数和 `add_vector` 函数的简单接口。下面是写入任务线程的完整代码，该线程从通道读取嵌入数据，分块并在达到一定大小时进行写入：

一旦数据写入，LanceDB 数据文件可以从任何进程中访问。下面是一个例子，展示如何使用相同的数据进行向量相似性搜索，使用的是 LanceDB 的 Python API，且该 API 可以从完全不同的进程中执行。

```py
uri = "data/vecdb1"
db = lancedb.connect(uri)
tbl = db.open_table("vectors_table_1")
# the vector we are finding similarities for
encoded_vec = get_some vector()
# perform a similiarity search for top 3 vectors
tbl.search(embeddings[0]) \
    .select(["filename"]) \
    .limit(3).to_pandas()
```

![](../Images/a82402bd8f2bfea7c2a6a5e986dedef9.png)

脚本输出（图片由作者提供）

# 5\. 结论

在这篇文章中，我们看到了一个使用 Rust、HuggingFace 的 Candle 框架和 LanceDB 的高性能 RAG 管道的工作示例。我们看到了如何将 Rust 的性能优势与 Candle 相结合，来高效地并行读取和嵌入多个文本文件。我们还看到了如何利用同步通道同时运行嵌入任务并与写入流程协同工作，而无需处理复杂的锁和同步机制。最后，我们学会了如何使用 Rust 利用 LanceDB 的高效存储，生成可以与多个 AI 框架和查询库集成的向量存储。

我相信这里概述的方法可以作为构建可扩展、生产就绪的 RAG 索引管道的强大基础。无论你是在处理大量数据，需要频繁更新知识库，还是在资源受限的环境中操作，本文讨论的构建块和设计原则都可以根据你的具体需求进行调整。随着 AI 领域的不断发展，高效地处理和检索相关信息的能力将始终至关重要。通过结合合适的工具和周到的设计，正如本文所展示的，开发人员可以创建不仅满足当前需求，而且能够应对未来 AI 驱动的信息检索和生成挑战的 RAG 管道。

备注与链接

+   GitHub 上的源代码可以在[这里](https://github.com/a-agmon/doc-embedder)找到。该仓库还包含了一个示例 jupyter notebook，展示了如何使用 Python 测试这一方法。

+   我之前关于 HuggingFace Candle 的文章可以在[这里](https://medium.com/towards-data-science/streamlining-serverless-ml-inference-unleashing-candle-frameworks-power-in-rust-c6775d558545)找到。

+   [Candle 框架](https://github.com/huggingface/candle)及其文档，包括他们的完整示例文件夹

+   [LanceDB](https://lancedb.com/)及其[Rust API 文档](https://docs.rs/lancedb/latest/lancedb/)
