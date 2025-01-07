# 如何以及为什么使用LLM进行基于块的信息检索

> 原文：[https://towardsdatascience.com/how-and-why-to-use-llms-for-chunk-based-information-retrieval-5242f0133b55?source=collection_archive---------4-----------------------#2024-10-28](https://towardsdatascience.com/how-and-why-to-use-llms-for-chunk-based-information-retrieval-5242f0133b55?source=collection_archive---------4-----------------------#2024-10-28)

[](https://medium.com/@peronc79?source=post_page---byline--5242f0133b55--------------------------------)[![Carlo Peron](../Images/e6db9521113aa6a2dd43b0b2aa6687b5.png)](https://medium.com/@peronc79?source=post_page---byline--5242f0133b55--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5242f0133b55--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5242f0133b55--------------------------------) [Carlo Peron](https://medium.com/@peronc79?source=post_page---byline--5242f0133b55--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5242f0133b55--------------------------------) ·阅读时长9分钟·2024年10月28日

--

![](../Images/ea9b1bb4b44a9f0b8ada220d93ecda54.png)

检索流程 — 图像来自作者

在本文中，我旨在解释为什么以及如何使用大语言模型（LLM）进行基于块的信息检索。

我以OpenAI的GPT-4模型为例，但这种方法可以应用于任何其他大语言模型，如Hugging Face、Claude等提供的模型。

每个人都可以免费访问这篇[文章](https://medium.com/@peronc79/5242f0133b55?sk=aafe7dca2cb777410b6e426321c0b53e)。

**标准信息检索的考虑事项**

主要概念是将一组文档（**文本块**）存储在数据库中，可以根据某些过滤条件进行检索。

通常，使用某种工具来实现混合搜索（例如Azure AI Search、LlamaIndex等），该工具可以：

+   使用诸如TF-IDF（例如BM25）之类的术语频率算法执行基于文本的搜索；

+   进行基于向量的搜索，计算向量之间的距离（通常是余弦相似度），即使使用不同的术语，也能识别相似的概念；

+   结合步骤1和步骤2的元素，通过加权突出最相关的结果。

![](../Images/6f4f4ba0a88c11c7dc09513356aa58d5.png)

图1 - 默认混合搜索流程 — 图像来自作者

图1显示了经典的检索流程：

+   用户向系统提出问题：“我想谈谈巴黎”；

+   系统接收问题，将其转换为嵌入向量（使用与摄取阶段相同的模型），并找到距离最小的文本块；

+   系统还执行基于频率的文本搜索；

+   来自两个过程的块经过进一步评估，并根据排名公式重新排序。

这个解决方案取得了良好的效果，但也有一些局限性：

+   并非所有相关的块都会被检索到；

+   有时，一些块包含异常值，影响最终的回答。

**典型检索问题的示例**

让我们考虑“documents”数组，它代表了一个知识库的示例，这个知识库可能导致不正确的块选择。

```py
documents = [
    "Chunk 1: This document contains information about topic A.",
    "Chunk 2: Insights related to topic B can be found here.",
    "Chunk 3: This chunk discusses topic C in detail.",
    "Chunk 4: Further insights on topic D are covered here.",
    "Chunk 5: Another chunk with more data on topic E.",
    "Chunk 6: Extensive research on topic F is presented.",
    "Chunk 7: Information on topic G is explained here.",
    "Chunk 8: This document expands on topic H. It also talk about topic B",
    "Chunk 9: Nothing about topic B are given.",
    "Chunk 10: Finally, a discussion of topic J. This document doesn't contain information about topic B"
]
```

假设我们有一个RAG系统，包含一个具有混合搜索能力的向量数据库和基于LLM的提示，用户向其提出如下问题：“我需要了解一些关于话题B的内容。”

如图2所示，搜索还返回了一个不正确的块，虽然语义上相关，但并不适合回答问题，在某些情况下，甚至可能会让负责提供答案的LLM感到困惑。

![](../Images/356ad8ff68ceb28acca4d8a23ec584c1.png)

图2 — 可能导致错误的检索示例 — 作者提供的图像

在这个示例中，用户请求关于“*话题B*”的信息，搜索返回的块包括“*该文档扩展了话题H，也涉及话题B*”以及“*与话题B相关的见解可以在这里找到*”，还有一个块指出，“*没有关于话题B的信息*”。

尽管这符合混合搜索的预期行为（因为块引用了“*话题B*”），但这并不是期望的结果，因为第三个块被返回时并没有识别到它对于回答问题并无帮助。

检索没有产生预期的结果，不仅仅因为BM25搜索在第三个块中找到了“*话题B*”这一术语，还因为向量搜索得到了较高的余弦相似度。

要理解这一点，请参见图3，它显示了使用OpenAI的text-embedding-ada-002模型进行嵌入时，块相对于问题的余弦相似度值。

![](../Images/c42a17cff143c55984745f79381a2c9e.png)

图3 — 使用text-embedding-ada-002的余弦相似度 — 作者提供的图像

很明显，“块9”的余弦相似度值是最高的，而且在该块与引用“*话题B*”的块10之间，还有一个块1，它没有提到“*话题B*”。

即使使用不同的方法衡量距离，这种情况也不会改变，正如在闵可夫斯基距离的情况下所见。

**利用LLM进行信息检索：一个示例**

我将描述的解决方案灵感来源于我在GitHub仓库中发布的内容 [https://github.com/peronc/LLMRetriever/](https://github.com/peronc/LLMRetriever/)。

这个想法是让LLM分析哪些块对回答用户的问题有用，而不是通过排名返回的块（如RankGPT的情况），而是直接评估所有可用的块。

![](../Images/ea9b1bb4b44a9f0b8ada220d93ecda54.png)

图4 - LLM检索管道 — 作者提供的图像

总结来说，如图4所示，系统接收一组需要分析的文档，这些文档可以来自任何数据源，如文件存储、关系数据库或向量数据库。

这些块被分成组，并通过与块总量成比例的线程数并行处理。

每个线程的逻辑包括一个循环，该循环遍历输入的块，为每个块调用OpenAI提示，以检查它与用户问题的相关性。

提示返回该块以及一个布尔值：*true* 如果它相关，*false* 如果它不相关。

**让我们开始编码吧 😊**

为了简化代码的解释，我将使用*documents*数组中存在的块（在结论部分我将引用一个实际案例）。

首先，我导入必要的标准库，包括os、langchain和dotenv。

```py
import os
from langchain_openai.chat_models.azure import AzureChatOpenAI
from dotenv import load_dotenv
```

接下来，我导入了我的LLMRetrieverLib/llm_retrieve.py类，它提供了几个执行分析所需的重要静态方法。

```py
from LLMRetrieverLib.retriever import llm_retriever
```

接下来，我需要导入使用Azure OpenAI GPT-4模型所需的必要变量。

```py
load_dotenv()
azure_deployment = os.getenv("AZURE_DEPLOYMENT")
temperature = float(os.getenv("TEMPERATURE"))
api_key  = os.getenv("AZURE_OPENAI_API_KEY")
endpoint = os.getenv("AZURE_OPENAI_ENDPOINT")
api_version = os.getenv("API_VERSION")
```

接下来，我继续初始化LLM。

```py
# Initialize the LLM
llm = AzureChatOpenAI(api_key=api_key, azure_endpoint=endpoint, azure_deployment=azure_deployment, api_version=api_version,temperature=temperature)
```

我们准备开始：用户提出问题以收集有关*主题B*的额外信息。

```py
question = "I need to know something about topic B"
```

在这一点上，相关块的搜索开始了，为此，我使用了 `LLMRetrieverLib/retriever.py` 库中的函数 `llm_retrieve.process_chunks_in_parallel`，该库也位于同一个代码库中。

```py
relevant_chunks = LLMRetrieverLib.retriever.llm_retriever.process_chunks_in_parallel(llm, question, documents, 3)
```

为了优化性能，函数 `llm_retrieve.process_chunks_in_parallel` 采用多线程来分配块分析到多个线程。

主要思想是为每个线程分配从数据库中提取的一个子集的块，并让每个线程根据用户的问题分析这些块的相关性。

处理结束时，返回的块完全符合预期：

```py
['Chunk 2: Insights related to topic B can be found here.',
'Chunk 8: This document expands on topic H. It also talk about topic B']
```

最后，我请求LLM为用户的问题提供答案：

```py
final_answer = LLMRetrieverLib.retriever.llm_retriever.generate_final_answer_with_llm(llm, relevant_chunks, question)
print("Final answer:")
print(final_answer) 
```

以下是LLM的回答，虽然内容相关，但由于这些块虽然相关，但在*主题B*的内容上并不充分，因此回答显得有些简单：

```py
Topic B is covered in both Chunk 2 and Chunk 8\. 
Chunk 2 provides insights specifically related to topic B, offering detailed information and analysis. 
Chunk 8 expands on topic H but also includes discussions on topic B, potentially providing additional context or perspectives.
```

**评分场景**

现在让我们尝试提出相同的问题，但使用基于评分的方法。

我请求LLM为每个块分配1到10的评分，以评估每个块与问题的相关性，仅考虑相关性高于5的块。

为此，我调用函数 `llm_retriever.process_chunks_in_parallel`，传入三个额外的参数，分别表示：将应用评分，必须大于或等于5才能被认为有效，并且我希望打印出带有各自评分的块。

```py
relevant_chunks = llm_retriever.process_chunks_in_parallel(llm, question, documents, 3, True, 5, True)
```

带评分的检索阶段产生了如下结果：

```py
score: 1 - Chunk 1: This document contains information about topic A.
score: 1 - Chunk 7: Information on topic G is explained here.
score: 1 - Chunk 4: Further insights on topic D are covered here.
score: 9 - Chunk 2: Insights related to topic B can be found here.
score: 7 - Chunk 8: This document expands on topic H. It also talk about topic B
score: 1 - Chunk 5: Another chunk with more data on topic E.
score: 1 - Chunk 9: Nothing about topic B are given.
score: 1 - Chunk 3: This chunk discusses topic C in detail.
score: 1 - Chunk 6: Extensive research on topic F is presented.
score: 1 - Chunk 10: Finally, a discussion of topic J. This document doesn't contain information about topic B
```

结果与之前相同，但评分很有趣 😊。

最后，我再次请求LLM为用户的问题提供答案，结果与之前的类似：

```py
Chunk 2 provides insights related to topic B, offering foundational information and key points.
Chunk 8 expands on topic B further, possibly providing additional context or details, as it also discusses topic H.
Together, these chunks should give you a well-rounded understanding of topic B. If you need more specific details, let me know!
```

**注意事项**

这种检索方法是通过一些先前的经验逐步发展出来的。

我注意到，纯粹基于向量的搜索虽然能产生有用的结果，但当嵌入在非英语语言中时，通常不够充分。

使用OpenAI处理意大利语句子时，明显发现术语的标记化常常不正确；例如，“*canzone*”一词在意大利语中意味着“*歌曲*”，但它被错误地标记为两个不同的词：“*can*”和“*zone*”。

这导致了嵌入数组的构建远未达到预期效果。

在这种情况下，混合搜索（同时结合了术语频率计数）能够改善结果，但它们并不总是如预期般准确。

因此，这种检索方法可以以下列方式使用：

+   **作为主要搜索方法：** 在这种方法中，数据库会根据过滤条件（例如元数据过滤器）查询所有片段或其子集；

+   **作为混合搜索中的一种优化：**（这是RankGPT使用的相同方法）通过这种方式，混合搜索可以提取大量片段，系统可以过滤它们，确保只有相关的片段传递到LLM，并且遵守输入令牌的限制；

+   **作为备选方案：** 在混合搜索没有得到期望结果的情况下，可以分析所有片段。

**让我们讨论成本和性能**

当然，闪闪发光的并不都是金子，因为必须考虑响应时间和成本。

在一个实际的使用案例中，我从一个关系型数据库中检索了由我的`LLMChunkizerLib/chunkizer.py`库语义分割的95个文本片段，这些片段来自两个Microsoft Word文档，总共33页。

对95个片段与问题相关性的分析是通过在本地PC上调用OpenAI的API完成的，该PC的带宽并不保证，平均约为10Mb，导致响应时间从7秒到20秒不等。

自然地，在云系统中，或通过在GPU上使用本地LLM，这些时间可以显著减少。

我认为，关于响应时间的考虑非常主观：在某些情况下，花更多时间来提供正确答案是可以接受的，而在其他情况下，至关重要的是不要让用户等待太久。

类似地，关于成本的考虑也非常主观，因为必须从更广泛的角度评估，是否提供尽可能准确的答案更为重要，还是一些错误是可以接受的。

在某些领域，由于不正确或遗漏的答案导致的声誉损害，可能超过了令牌的费用。

此外，尽管OpenAI和其他提供商的成本在近年来一直在稳步下降，但那些已经拥有基于GPU的基础设施的人（可能是由于需要处理敏感或机密数据）可能更倾向于使用本地的LLM。

**结论**

总之，我希望能提供我的视角，分享如何进行检索的思考。

如果没有别的，我的目标是提供帮助，并可能激励他人在自己的工作中探索新方法。

记住，信息检索的世界广阔无垠，只要稍加创意和借助合适的工具，我们就能以我们从未想象过的方式发掘知识！

如果你想进一步讨论，欢迎通过[LinkedIn](https://www.linkedin.com/in/carlo-peron)与我联系。

GitHub 仓库可以在这里找到：

• [https://github.com/peronc/LLMRetriever/](https://github.com/peronc/LLMRetriever/)

• [https://github.com/peronc/LLMChunkizer/](https://github.com/peronc/LLMChunkizer/)
