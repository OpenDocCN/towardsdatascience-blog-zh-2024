# 提升你的RAG的高级检索技术

> 原文：[https://towardsdatascience.com/advanced-retriever-techniques-to-improve-your-rags-1fac2b86dd61?source=collection_archive---------0-----------------------#2024-04-17](https://towardsdatascience.com/advanced-retriever-techniques-to-improve-your-rags-1fac2b86dd61?source=collection_archive---------0-----------------------#2024-04-17)

## 精通高级信息检索：利用**Langchain**优化相关文档选择的前沿技术，打造卓越的RAG

[](https://medium.com/@damiangilgonzalez?source=post_page---byline--1fac2b86dd61--------------------------------)[![Damian Gil](../Images/8b378c321ee21b0bd40faa14db7e9487.png)](https://medium.com/@damiangilgonzalez?source=post_page---byline--1fac2b86dd61--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1fac2b86dd61--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1fac2b86dd61--------------------------------) [Damian Gil](https://medium.com/@damiangilgonzalez?source=post_page---byline--1fac2b86dd61--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1fac2b86dd61--------------------------------) ·18分钟阅读·2024年4月17日

--

# 目录

**·** [**引言**](#a896) **·** [**向量存储创建**](#d7a4) **·** [**方法：朴素检索器**](#10a9) **·** [**方法：父文档检索器**](#1f6d) **·** [**方法：自查询检索器**](#b2c7)∘ [查询构造器](#a483)

∘ [查询翻译器](#21ad) **·** [**方法：上下文压缩检索器（重排名）**](#8232) **·** [**结论**](#3c63)

# 引言

***让我们简要回顾一下构成RAG这个词的三个缩写的含义：***

+   **检索**：RAG的主要目标是收集与查询相关的最相关文档/片段。

+   **增强**：创建一个结构良好的提示，这样当调用LLM时，它能完全理解自己的目的、上下文以及应如何回应。

+   **生成**：这是大语言模型（LLM）发挥作用的地方。当模型提供了良好的上下文（由“检索”步骤提供）并且有清晰的指令（由“增强”步骤提供）时，它将为用户生成高价值的响应。

正如我们所看到的，生成对用户查询的响应（如果我们应用RAG进行问答目的）直接依赖于我们如何构建“*增强*”部分，特别是“*检索*”部分。

**在本文中，我们将专注于“*检索*”部分**。在这一重要的返回最相关文档的过程中，向量存储的概念会出现。

![](../Images/a581156c21a7c624ecde0048b05ffc5c.png)

本文展示的技术概述（图像来自作者）。

为了创建这些检索，我们将使用Langchain库。

![](../Images/3182127071644529aa590a1862f565f2.png)

本文中使用的技术概览（图片由作者提供）。

向量存储就是一个向量数据库，用于存储以向量格式表示的文档。这种向量表示来自于transformers的使用。我并不是说一些你此刻不知道的事情。

很显然，这个向量存储越强大、越完整，我们能运行的检索器就越好。我们已经知道，创建这个数据库本身就是一门艺术。根据我们使用的块大小或嵌入模型，RAG的效果会有所不同。

在此我做出一个说明：

> 在这篇文章中，我们不会讨论如何创建这个向量存储。
> 
> 在这篇文章中，我们将讨论一些用于检索相关文档的技术。

既然一图胜千言，我建议你查看以下内容：

![](../Images/7a733d4eae52fcc8376c3438a97622fc.png)

一个RAG包含一系列明确的步骤。本文只会介绍检索器部分（图片由作者提供）。

因此，我再次强调，在这篇文章中，我们将深入研究创建一个优秀RAG工具的众多重要步骤之一。 “*检索*”步骤至关重要，因为它直接影响LLM生成响应时的上下文。

我们将研究的方法是：

+   **朴素检索器**

+   **父文档检索器**

+   **自查询检索器**

+   **上下文压缩检索器（重新排序）**

你可以在[**这里**](https://github.com/damiangilgonzalez1995/AdvancedRetrievalRags/tree/main)找到包含笔记本的项目**。** 你也可以查看我的github：

[](https://github.com/damiangilgonzalez1995?source=post_page-----1fac2b86dd61--------------------------------) [## damiangilgonzalez1995 - 概览

### 对数据充满热情，我从物理学转向了数据科学。曾在Telefonica、HP工作，现在是…

github.com](https://github.com/damiangilgonzalez1995?source=post_page-----1fac2b86dd61--------------------------------)

# 向量存储创建

为了更好地阐释这些方法，将通过一个实际案例进行讲解。因此，我们将创建一个关于《约翰·威克》电影评论的RAG。

为了让读者能跟随本文的每一个步骤，他们可以访问我创建的仓库。在里面，你会找到每种方法的代码，以及用于创建向量存储的文档。负责此任务的jupyter notebook可以在git仓库中找到，文件名为“[0_***create_vectore_db.ipynb***](https://github.com/damiangilgonzalez1995/AdvancedRetrievalRags/blob/main/0_create_vectore_db.ipynb)”。

关于我们RAG的数据来源，有4个csv文件，每个文件对应约翰·威克系列电影的每部电影的评论。这些文件包含以下信息：

![](../Images/be5401cfc1f8f45719490ff05aa9528b.png)

项目的数据集（图片由作者提供）。

如你所见，“**评论**”字段将是我们检索器的目标。其他字段作为元数据进行存储也很重要：

+   **电影标题**

+   **评论日期**

+   **评论标题**

+   **评论网址**

+   **作者**

+   **评分**

要将我们文件的每一行读取并转换为“*文档*”格式，我们执行以下代码：

```py
from langchain_community.document_loaders.csv_loader import CSVLoader
from datetime import datetime, timedelta

documents = []

for i in range(1, 4):
  loader = CSVLoader(
    encoding="utf8",
    file_path=f"data/john_wick_{i}.csv",
    metadata_columns=["Review_Date", "Review_Title", "Review_Url", "Author", "Rating"]
  )

  movie_docs = loader.load()
  for doc in movie_docs:

    # We add metadate about the number of the movi
    doc.metadata["Movie_Title"] = f"John Wick {i}"

    # convert "Rating" to an `int`, if no rating is provided - None
    doc.metadata["Rating"] = int(doc.metadata["Rating"]) if doc.metadata["Rating"] else 5

  documents.extend(movie_docs)
```

我们已经有了“*文档*”格式的文档：

```py
print(documents[0])

Document(page_content=": 0\nReview: The best way I can describe John Wick is to picture Taken but instead of Liam Neeson it's Keanu Reeves and instead of his daughter it's his dog. That's essentially the plot of the movie. John Wick (Reeves) is out to seek revenge on the people who took something he loved from him. It's a beautifully simple premise for an action movie - when action movies get convoluted, they get bad i.e. A Good Day to Die Hard. John Wick gives the viewers what they want: Awesome action, stylish stunts, kinetic chaos, and a relatable hero to tie it all together. John Wick succeeds in its simplicity.", metadata={'source': 'data/john_wick_1.csv', 'row': 0, 'Review_Date': '6 May 2015', 'Review_Title': ' Kinetic, concise, and stylish; John Wick kicks ass.\n', 'Review_Url': '/review/rw3233896/?ref_=tt_urv', 'Author': 'lnvicta', 'Rating': 8, 'Movie_Title': 'John Wick 1', 'last_accessed_at': datetime.datetime(2024, 4, 8, 11, 49, 47, 92560)})
```

我们只需要在本地创建一个向量数据库（**向量存储**）。为此，我使用了**Chroma**。同时，记住需要使用一个嵌入模型，它将把我们的文档转换为向量格式进行存储。所有这些内容可以在以下代码片段中看到：

```py
from langchain_community.vectorstores import Chroma
from langchain_openai import OpenAIEmbeddings
import os
from dotenv import load_dotenv

load_dotenv()
os.environ["OPENAI_API_KEY"] = os.getenv('OPENAI_KEY')

embeddings = OpenAIEmbeddings(model="text-embedding-3-small")

db = Chroma.from_documents(documents=documents, embedding=embeddings, collection_name="doc_jonhWick", persist_directory="./jonhWick_db")
```

这将在我们的服务器上创建一个名为“***JonhWick_db***”的数据库。这个数据库将被我们的RAG使用，检索器将从中获取与用户查询最相关的文档。

现在是时候介绍创建检索器的不同方法了。

# **方法：朴素检索器**

> 代码见[1_naive_retriever.ipynb](https://github.com/damiangilgonzalez1995/AdvancedRetrievalRags/blob/main/1_naive_retriever.ipynb)文件。

这种方法是最简单的，实际上它的名字就说明了这一点。我们使用这个形容词来标识这种方法，原因很简单，当我们将查询输入到数据库时，我们（天真地）希望它返回最相关的文档/片段。

基本上发生的情况是，我们使用与创建向量存储时相同的转换器对用户查询进行编码。一旦获得其向量表示后，我们通过计算余弦相似度、距离等来计算相似性。

> 我们收集与查询最相似的前K个文档。

这种类型的检索器流程可以在下图中看到：

![](../Images/fdf9e144f28b71307dd78345689f6d41.png)

**朴素检索器**的简化表示（图像由作者提供）。

记住这个方案，我们来看看所有这些在代码中的表现。我们读取数据库：

```py
from langchain_community.vectorstores import Chroma
from langchain_openai import OpenAIEmbeddings
import os
from dotenv import load_dotenv

load_dotenv()
os.environ["OPENAI_API_KEY"] = os.getenv('OPENAI_KEY')

embeddings = OpenAIEmbeddings(model="text-embedding-3-small")

vectordb= Chroma(persist_directory="./jonhWick_db", 
                  embedding_function=embeddings, 
                  collection_name="doc_jonhWick")pyth
```

然后我们创建我们的***检索器***。我们可以配置相似性计算方法以及其他参数。

> **检索器**

```py
# Specifying top k
naive_retriever = vectordb.as_retriever(search_kwargs={ "k" : 10})

# Similarity score threshold retrieval
# naive_retriever = db.as_retriever(search_kwargs={"score_threshold": 0.8}, search_type="similarity_score_threshold")

# Maximum marginal relevance retrieval
# naive_retriever = db.as_retriever(search_type="mmr")
```

实际上，我们已经创建了我们的“***朴素检索器***”，但为了看看它是如何工作的，我们将创建完整的RAG，回顾一下它由以下组件组成：

+   *R（检索）*: **已完成**

+   *A（增强）*: **尚未完成**

+   *G（生成）*: **尚未完成**

> **增强与生成**

```py
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI

# Augmented
TEMPLATE = """\
You are happy assistant. Use the context provided below to answer the question.

If you do not know the answer, or are unsure, say you don't know.

Query:
{question}

Context:
{context}
"""

rag_prompt = ChatPromptTemplate.from_template(TEMPLATE)

# Generation
chat_model = ChatOpenAI()
```

我们已经拥有了RAG的三个组件。剩下的就是将它们组装起来，为此我们将使用Langchain的链来创建RAG。

我不知道你是否了解Langchain为更高效地创建链而设计的语言。这种语言被称为**LCEL（LangChain表达语言）**。如果你对这种创建Langchain链的方式不熟悉，我在这里为你提供一个非常好的教程：

最后，我们使用Langchain自有的链创建语言（***LCEL***）来创建我们的RAG：

```py
from langchain_core.runnables import RunnablePassthrough, RunnableParallel
from operator import itemgetter
from langchain_core.output_parsers import StrOutputParser

setup_and_retrieval = RunnableParallel({"question": RunnablePassthrough(), "context": naive_retriever })
output_parser = StrOutputParser()

naive_retrieval_chain = setup_and_retrieval 
                        | rag_prompt 
                        | chat_model 
                        | output_parser

naive_retrieval_chain.invoke( "Did people generally like John Wick?")

# response: 'Yes, people generally liked John Wick.' 
```

这是为RAG创建链条的最简单方法。在Jupyter notebook中，你可以找到同样的链条，但更为强大。因为我不希望我们现在在这个话题上迷失，所以我只展示了最简单的形式。同时，为了让我们理解上面代码的内容，我创建了这个非常清晰的图示：

![](../Images/8c020ccc0b269e852d9b957ec4899227.png)

使用Langchain创建RAG及其LCEL语言（图片来自作者）。

太好了，我们完成了创建我们的***简单RAG***。接下来让我们进入下一个方法。

# 方法：父文档检索器

> 在[2_parent_document_retriever.ipynb](https://github.com/damiangilgonzalez1995/AdvancedRetrievalRags/blob/main/2_parent_document_retriever.ipynb)文件中的代码。

假设我们创建了一个RAG来通过输入一些症状来识别可能的疾病。如果我们使用的是一个简单的RAG，我们可能会收集到一系列只在一两个症状上相符的可能疾病，从而使我们的工具陷入困境。

这是使用父文档检索器的理想案例。这种技术的类型包括将大块（父块）切分成更小的块（子块）。通过拥有小块，它们所包含的信息更加集中，因此，它的信息价值不会在文本段落之间被稀释。

在这一切中有一个小问题：

+   如果我们希望在搜索最相关的文档时更加精确，我们需要将文档分割成***更小的块***。

+   但同样重要的是为LLM提供良好的上下文，这可以通过提供***更大的块***来实现。

之前所说的内容可以通过以下图片看到：

![](../Images/8413c96f449f1fa9ad87a95c0951e5bc.png)

这两种概念/度量之间平衡的表现（图片来自作者）。

看起来似乎没有解决问题的方法，因为当我们提高精度时，上下文会减少，反之亦然。这时，这种方法出现了，它将解决我们的困境。

主要思路是将大块（**父块/文档**）进一步切分成更小的块（**子块/文档**）。一旦完成这一点，就可以使用子块进行最相关的前K个文档的搜索，并返回属于这些前K个子文档的父块。

我们已经有了主要想法，现在让我们将其具体化。最好的解释方式是一步一步来：

1.  获取文档并创建大块（**父块**）。

1.  对每个父块进行分割以生成**子块**。

1.  将子块（*向量表示*）保存在**向量存储**中。

1.  将***父块保存在内存中***（我们不需要创建它们的向量表示）。

之前所说的内容可以通过以下图片看到：

![](../Images/c6ef2ddb4cb287519a2c1535eb7c79c9.png)

这是如何从**父块**创建**子块**并进行存储的可视化表示。这些步骤是创建**父文档检索器**的必要步骤（图片来自作者）。

这看起来可能非常复杂，因为我们需要创建一个新的数据库，包含小块，且将父块保存在内存中。此外，还需要知道每个子块属于哪个父块。谢天谢地，**Langchain**的存在使得构建过程变得非常简单。

你一定已经得出结论，创建这种方法需要一个新的向量存储。此外，在像约翰·威克电影评论这样的数据源（如CSV文件）中，不需要进行第一次切分（父块）。这是因为我们可以将每一行CSV文件看作是一个独立的块。

总的来说，让我们通过以下图像来可视化这一方法的工作原理：

![](../Images/96d669987379cb97201196e3a60d3d33.png)

**父文档检索器**的工作原理的可视化表示（图片来自作者）。

编写代码的方式如下所示：

```py
from langchain.retrievers import ParentDocumentRetriever
from langchain.storage import InMemoryStore
from langchain_text_splitters import RecursiveCharacterTextSplitter
from langchain_openai import OpenAIEmbeddings
from langchain_community.vectorstores import Chroma

# documents = Read csv files. Check jupyter notebook for more details

parent_docs = documents

# Embedding Model
embeddings = OpenAIEmbeddings(model="text-embedding-3-small")

# Splitters
child_splitter = RecursiveCharacterTextSplitter(chunk_size=200)
# We don't need a parent splitter because the data cames from CSV file, and each row is a parent doc.
# parent_splitter = RecursiveCharacterTextSplitter(chunk_size=800)

# Stores
store = InMemoryStore()
vectorstore = Chroma(embedding_function=embeddings, collection_name="fullDoc", persist_directory="./JohnWick_db_parentsRD")

parent_document_retriever = ParentDocumentRetriever(
    vectorstore=vectorstore,
    docstore=store,
    child_splitter=child_splitter,
    # parent_splitter =parent_splitter
)
```

这里直观的一个概念是，**向量存储中的块数（子块的数量）应该远远高于存储在内存中的文档数量（父块的数量）**。我们可以通过以下代码进行检查：

```py
print(f"Number of parent chunks  is: {len(list(store.yield_keys()))}")

print(f"Number of child chunks is: {len(parent_document_retriever.vectorstore.get()['ids'])}")

'''
Number of parent chunks  is: 75
Number of child chunks is: 3701
'''
```

很好，我们已经有了我们的**父文档检索器**，接下来只需要基于这个检索器创建我们的RAG，完成方法与之前一样。我附上了在langchain中创建链条的代码。想查看更多细节，可以查看[jupyter notebook](https://github.com/damiangilgonzalez1995/AdvancedRetrievalRags/blob/main/2_parent_document_retriever.ipynb)。

```py
setup_and_retrieval = RunnableParallel({"question": RunnablePassthrough(), "context": parent_document_retriever })
output_parser = StrOutputParser()

parent_retrieval_chain = setup_and_retrieval | rag_prompt | chat_model | output_parser
```

请注意，这与之前的情况完全相同，只是有一个小区别，即在***“setup_and_retrieval”***变量中，我们配置了使用我们的***“parent_document_retriever”***，而不是***“naive_retriever”***。

# 方法：自查询检索器

> [3_self_query_retriever.ipynb](https://github.com/damiangilgonzalez1995/AdvancedRetrievalRags/blob/main/3_self_query_retriever.ipynb)文件中的代码。

这可能是提高我们检索器效率的最优化方法之一。

> 它的主要特点是能够在向量存储中执行搜索，并应用基于元数据的过滤器。

我们知道，当我们应用“**朴素检索**”时，我们在计算向量数据库中所有块与查询的相似度。向量存储的块越多，需要进行的相似度计算就越多。现在，试想一下如果能先基于**元数据进行过滤**，然后在选择出符合元数据条件的块之后，再进行相似度计算。**这可以极大地减少计算和时间成本。**

让我们通过一个用例来全面理解何时应用这种类型的检索。

> 假设我们在向量数据库中存储了大量的体验和休闲活动（例如：冲浪课程、滑索、 gastronomic 路线等）。这些体验的描述是我们通过嵌入模型编码的。此外，每个活动都有三个关键值或元数据：日期、价格和地点。
> 
> 假设用户正在寻找一种这样的体验：一种适合全家人且安全的自然体验。此外，价格必须低于$50，地点在加利福尼亚。

有一点是明确的

> **我们不希望你返回不符合用户请求的价格或地点的活动/体验。**

因此，计算与不符合元数据过滤器要求的片段/体验的相似度是没有意义的。

这个案例非常适合应用***自查询检索器（Self Query Retriever）***。这种类型的检索器允许我们首先通过元数据进行过滤，然后再在符合元数据要求的片段和用户输入之间进行相似度计算。

这个技术可以总结为两个非常具体的步骤：

+   **查询构造器**

+   **查询翻译器**

## **查询构造器**

“***查询构造器***”步骤的目标是**根据用户输入创建适当的查询和过滤器。**

谁负责应用相应的过滤器？你怎么知道它们是什么？

为此，我们将使用一个LLM（大型语言模型）。这个LLM必须能够决定何时以及应用哪些过滤器。我们还需要事先解释元数据是什么以及它们的含义。简而言之，提示（prompt）必须包含三个关键点：

+   **上下文**：个性，应该如何行动，输出格式等。

+   **元数据**：有关可用元数据的信息。

+   **查询**：用户的查询/输入/问题。

LLM生成的输出不能直接输入到数据库中。因此，需要所谓的“***查询翻译器***”。

## 查询翻译器

这是一个负责**将LLM（查询构造器）的输出转换为适当格式以执行查询的模块**。根据你使用的向量数据库，你将需要使用不同的模块。以我为例，我使用的是**Chroma db**，因此，我需要一个针对这个数据库的翻译器。幸运的是，Langchain几乎为所有数据库提供了特定的数据库翻译器。

正如你可能已经注意到的，我是一个图表的忠实粉丝。让我们看看以下图示，它能为这个问题提供相当清晰的解释：

![](../Images/4f316e099990fd95c123b5e3de6d15ee.png)

**自查询检索器**的工作原理的视觉表示（图片来自作者）。

关于前面的图像，我们看到一切从用户的查询开始。我们创建一个包含 3 个关键字段的提示，并将其传递给 LLM，LLM 生成一个包含两个关键字段的响应：“***Query***” 和 “***Filter***”。这个响应被传递到查询翻译器，查询翻译器将这两个字段转换为 ***Chroma DB*** 所需的正确格式。执行查询并根据用户最初的问题返回最相关的文档。

需要强调的是，用户输入的查询不必与输入数据库的查询相同。在图示中可以看到，LLM 根据**可用元数据和用户的提问**，发现可以使用“评级”元数据创建一个过滤器。它还根据用户的查询创建一个新的查询。

让我们看看代码中的实现。如我所解释的，提供一个详细描述向量库中可用元数据的信息对 LLM 来说非常重要。这可以通过以下代码段来实现：

```py
from langchain.chains.query_constructor.base import AttributeInfo
from langchain.retrievers.self_query.base import SelfQueryRetriever
from langchain_openai import ChatOpenAI
from langchain.retrievers.self_query.chroma import ChromaTranslator

metadata_field_info = [
    AttributeInfo(
        name="Movie_Title",
        description="The title of the movie",
        type="string",
    ),
    AttributeInfo(
        name="Review_Date",
        description="The date of the review",
        type="string",
    ),
    AttributeInfo(
        name="Review_Title",
        description="The title of the review",
        type="string",
    ),
    AttributeInfo(
        name="Review_Url",
        description="The URL of the review",
        type="string",
    ),
    AttributeInfo(
        name="Author",
        description="The author of the review",
        type="string",
    ),
    AttributeInfo(
        name="Rating",
        description="A 1 to 10 rating for the movie",
        type="integer",
    )
]
```

要定义我们的检索方法，我们必须定义以下几个要点：

+   要使用的 LLM

+   要使用的嵌入模型

+   访问的向量库

+   该文件的描述，说明可以找到哪些信息

    该向量库中的文档。

+   元数据描述

+   您希望使用的查询翻译器

让我们看看代码中的实现：

```py
document_content_desription = "A review of the Jonh Wick movie."
embeddings = OpenAIEmbeddings(model="text-embedding-3-small")
chat_model = ChatOpenAI()

self_query_retriever = SelfQueryRetriever.from_llm(
    llm=ChatOpenAI(temperature=0),
    vectorstore =vectordb,
    document_contents = document_content_desription,
    metadata_field_info =metadata_field_info,
    verbose = True,
    structured_query_translator = ChromaTranslator()
)
```

让我们通过一个非常清晰的例子来看看，如何通过使用这种类型的检索器大大改进我们的 RAG。**首先我们使用一个简单的检索器，然后使用自查询检索器。**

```py
Question = "Make a summary of the reviews that talk about John Wick 3 and have a score higher than 7"
response = naive_retrieval_chain.invoke(Question)
print(response)

'''
I don't know the answer.
'''
------------------------------------------------------------------------

response = self_retrieval_chain.invoke(Question)
print(response)

'''
John Wick: Chapter 3 - Parabellum is quite literally 
about consequences, dealing with the fallout of John's...
'''
```

正如我们所见，效果显著提升。

# 方法：上下文压缩检索器（重新排序）

> 代码见 [4_contextual_compression_retriever(reranking).ipynb](https://github.com/damiangilgonzalez1995/AdvancedRetrievalRags/blob/main/4_contextual_compression_retriever(reranking).ipynb) 文件。

+   **上下文窗口**：我们从向量库中获取的文档越多，**LLM 获得的信息就越多**，从而能够给出更好的答案。

+   **召回率**：从向量库中检索的文档越多，获取到**不相关片段的概率越大，因此召回率会增加**（这不是一个好事）。

似乎没有解决这个问题的方法。当我们在某个指标上提高性能时，另一个指标似乎注定要变差。***我们确定吗？***

这就是压缩检索器技术的应用，重点是重新排序技术。可以说，这项技术由两个截然不同的步骤组成：

+   **步骤 1**：根据输入/问题获取大量相关文档。通常我们会设置最相关的 K 个文档。

+   **步骤 2**：重新计算这些文档中哪些是真正相关的，丢弃那些不太有用的文档（**压缩**）。

对于第一步，使用的是所谓的**双编码器（Bi-Encoder）**，这实际上就是我们通常用来进行基本RAG的方式。将文档向量化，将查询向量化，并使用我们选择的任何度量计算相似度。

第二步与我们通常看到的有所不同。这一重新计算/重新排序由**重新排序模型**或**跨编码器**执行。

> **这些模型期望接受两个文档/文本作为输入，并返回它们之间的相似度评分。**

如果这两种输入中的一个是**查询**，另一个是**文档块**，我们可以计算它们之间的相似度。

这两种方法可以如下展示：

![](../Images/733f0ca8db289656a32f5b509bea13e2.png)

计算文本之间相似度的两种方法的视觉表示（图像来源：作者）。

你会发现这两种方法最终提供了相同的结果，即反映两个文本之间相似度的度量。这完全正确，但有一个关键特点：

> 跨编码器返回的结果比双编码器更加可靠。

好的，它的效果更好，因为我们不会直接用所有文档块，而是只用前K个块。因为直接使用所有文档块将是***在时间和金钱/计算上的巨大开销***。因此，我们会**首先筛选出与查询最相似的文档块，** **将重新排序模型的使用限制在K次以内。**

一个好的问题是：在哪里可以找到跨编码器（Cross-Encoder）模型？幸运的是，我们可以在[HuggingFace](https://huggingface.co/cross-encoder)找到开源模型，但在本文的实际案例中，我们将使用由公司[Cohere](https://cohere.com)提供的模型。

[](https://cohere.com/?source=post_page-----1fac2b86dd61--------------------------------) [## Cohere | 企业领先的人工智能平台

### Cohere提供行业领先的大型语言模型（LLMs）和RAG功能，专门为满足…

cohere.com](https://cohere.com/?source=post_page-----1fac2b86dd61--------------------------------)

为了更好地理解这种方法的架构，我们来看一个视觉示例。

![](../Images/f6e6821a5c1c17bfdc9eb5d3c0fadc87.png)

**上下文压缩检索器（重新排序）**的视觉表示（图像来源：作者）。

该图像显示了步骤：

+   **1º)** 我们获取查询，将其用变换器编码为向量形式，并将其输入到向量数据库中。

+   **2º)** 从我们的数据库中收集**与查询最相似的文档**。我们可以使用任何检索方法。

+   **3º)** 接下来我们使用Cohere的跨编码器模型。在图中的示例中，这个模型将被使用4次。记住，**这个模型的输入将是查询和一个文档/文档块，用来计算这两篇文本的相似度。**

+   **4º)** 在之前的步骤中，已经对该模型进行了4次调用，并获得了查询与每个文档之间的相似度值（介于0到1之间）。如图所示，之前步骤中获得的第1块数据，在重排序后，现在排在第4位。

+   **5º)** 我们添加了与上下文最相关的前三个块。

再次回到计算成本和时间，如果直接应用交叉编码器，考虑到每次**新查询时，都需要计算查询与每个文档的相似度**，这显然是非常低效的。

另一方面，使用**双编码器时，文档的向量表示对于每个新查询都是相同的。**

然后，我们有了一个更为优越的方法，虽然执行起来很昂贵，但另一方面，另一个方法运行良好，并且每个新查询的计算成本不大。所有这些都得出了统一这两种方法以改进RAG的结论。这就是所谓的***带有重排序的上下文压缩方法***。

让我们继续进行代码部分。让我们记住，这种方法使用了一个检索器，在我们的案例中将是一个简单检索器：

```py
naive_retriever = vectordb.as_retriever(search_kwargs={ "k" : 10})
```

由于**Langchain**和其与**Cohere**的集成，我们只需要导入一个模块，该模块将执行对Cohere交叉编码器模型的调用：

```py
from langchain_cohere import CohereRerank

os.environ["COHERE_API_KEY"] = "YOUR API KEY FROM COHERE"

compressor = CohereRerank(top_n=3)
```

最后，我们创建了我们的**带有Langchain的上下文压缩检索器**：

```py
from langchain.retrievers.contextual_compression import ContextualCompressionRetriever

compression_retriever = ContextualCompressionRetriever(
    base_compressor=compressor, 
    base_retriever=naive_retriever
)
```

就这么简单。让我们来比较一下***简单检索器和重排序检索器***：

![](../Images/70e6c3fa51c7f2afef9abf607072c3e8.png)

这是重排序方法如何重新计算查询与块之间相似度的示例。这导致第一个检索器返回的最相关文档（在我们的案例中是简单检索器）被完全重新排序。前三个最相关的文档被收集，如所示（图片来自作者）。

如我们所见，简单检索器返回了前10个块/文档。经过重排序并获取到最相关的3个文档/块后，发生了显著变化。注意，文档**编号16**，在第一次检索器中是**第三位**，在进行重排序后，**变成了第一位**。

# 结论

我们已经看到，根据我们想要应用RAG的案例的特点，我们可能会选择不同的方法。此外，也可能出现不知道使用哪种检索器方法的情况。为此，存在不同的库来评估你的RAG。

为此目的有几种工具可供选择。我个人推荐的几个选项是[RAGAS](https://docs.ragas.io/en/stable/)和[LangSmith](https://www.langchain.com/langsmith)的组合。

[](https://blog.langchain.dev/evaluating-rag-pipelines-with-ragas-langsmith/?source=post_page-----1fac2b86dd61--------------------------------) [## 使用Ragas + LangSmith评估RAG管道

### 编辑备注：本文由Ragas团队共同撰写。我们讨论和思考的一个问题是……

blog.langchain.dev](https://blog.langchain.dev/evaluating-rag-pipelines-with-ragas-langsmith/?source=post_page-----1fac2b86dd61--------------------------------)

我强烈推荐关注、学习并观看这些人制作的视频，他们正是启发我写这篇文章的来源。

[## AI 创客空间  

### 与我们一起学习如何构建、部署和分享生产级大语言模型应用！

www.youtube.com](https://www.youtube.com/@AI-Makerspace?source=post_page-----1fac2b86dd61--------------------------------)

*感谢阅读！*

*如果你觉得我的工作有用，可以订阅* [***每次我发布新文章时都会收到邮件***](https://medium.com/@damiangilgonzalez/subscribe)***。***

*如果你愿意，* [***在LinkedIn上关注我***](https://www.linkedin.com/in/damiangilgonzalez/)
