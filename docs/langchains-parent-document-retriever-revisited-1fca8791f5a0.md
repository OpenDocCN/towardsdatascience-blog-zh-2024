# LangChain的父文档检索器 — 重新审视

> 原文：[https://towardsdatascience.com/langchains-parent-document-retriever-revisited-1fca8791f5a0?source=collection_archive---------4-----------------------#2024-07-22](https://towardsdatascience.com/langchains-parent-document-retriever-revisited-1fca8791f5a0?source=collection_archive---------4-----------------------#2024-07-22)

## 仅使用你的向量数据库增强上下文检索

[](https://medium.com/@omri-levy?source=post_page---byline--1fca8791f5a0--------------------------------)[![Omri Eliyahu Levy](../Images/7200edb7c45b097970034fc6f740a8bd.png)](https://medium.com/@omri-levy?source=post_page---byline--1fca8791f5a0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1fca8791f5a0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1fca8791f5a0--------------------------------) [Omri Eliyahu Levy](https://medium.com/@omri-levy?source=post_page---byline--1fca8791f5a0--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1fca8791f5a0--------------------------------) ·6分钟阅读·2024年7月22日

--

**TL;DR** — 我们通过使用元数据查询实现与LangChain的父文档检索器相同的功能（[链接](https://python.langchain.com/v0.1/docs/modules/data_connection/retrievers/parent_document_retriever/)）。你可以在[这里](https://gist.github.com/omriel1/7243ce233eb2986ed2749de6ae79ecb7)查看代码。

# RAG简介

检索增强生成（RAG）目前是LLM和AI应用领域最热门的话题之一。

简而言之，RAG是一种将生成模型的响应基于选定知识源进行“立足”的技术。它包括两个阶段：检索和生成。

1.  在检索阶段，给定用户的查询，我们从预定义的知识源中检索相关信息。

1.  然后，我们将检索到的信息插入到发送给LLM的提示中，该模型（理想情况下）根据提供的上下文生成对用户问题的回答。

一种常用的方法来实现高效准确的检索是通过使用嵌入。在这种方法中，我们通过将文档拆分成块（例如页面、段落或句子）来预处理用户的数据（假设为纯文本）。然后，我们使用嵌入模型为这些块创建有意义的数值表示，并将它们存储在向量数据库中。现在，当查询到来时，我们也对其进行嵌入，并使用向量数据库执行相似性搜索，以检索相关信息。

![](../Images/bba1888e550d749b3f7d751f75b4661a.png)

图片由作者提供

如果您完全不熟悉这个概念，我推荐[deeplearning.ai](https://www.deeplearning.ai/)的精彩课程，[LangChain: 与您的数据对话](https://www.deeplearning.ai/short-courses/langchain-chat-with-your-data/)。

# 什么是“父文档检索”？

“父文档检索”或其他人所称的“句子窗口检索”，是一种通过提供更广泛的上下文供 LLM 考虑来增强 RAG 检索方法性能的常见方法。

本质上，我们将原始文档分成相对较小的块，嵌入每一个块，并将它们存储在向量数据库中。使用这些小块（一句话或几句话）有助于嵌入模型更好地反映其含义[1]。

然后，**在检索时，我们不仅返回由向量数据库找到的最相似块，还返回其在原文档中的周围上下文**（块）**。这样，LLM 将拥有更广泛的上下文，在许多情况下有助于生成更好的答案。**

LangChain 通过父文档检索器[2]支持这一概念。父文档检索器允许您：(1) 检索特定块来源的完整文档，或 (2) 为与父文档关联的每个较小块预先定义一个更大的“父”块。

让我们从[LangChains 文档](https://python.langchain.com/v0.1/docs/modules/data_connection/retrievers/parent_document_retriever/)中探讨一个例子：

```py
# This text splitter is used to create the parent documents
parent_splitter = RecursiveCharacterTextSplitter(chunk_size=2000)
# This text splitter is used to create the child documents
# It should create documents smaller than the parent
child_splitter = RecursiveCharacterTextSplitter(chunk_size=400)
# The vectorstore to use to index the child chunks
vectorstore = Chroma(
    collection_name="split_parents", embedding_function=OpenAIEmbeddings()
)
# The storage layer for the parent documents
store = InMemoryStore()
retriever = ParentDocumentRetriever(
    vectorstore=vectorstore,
    docstore=store,
    child_splitter=child_splitter,
    parent_splitter=parent_splitter,
)
retrieved_docs = retriever.invoke("justice breyer")
```

在我看来，LangChains 方法有两个缺点：

1.  需要管理外部存储，以便利用这一有用的方法，无论是内存还是其他持久化存储。当然，对于真实的使用案例，多个示例中使用的 InMemoryStore 是不够的。

1.  “父文档”检索不是动态的，意味着我们无法动态调整周围窗口的大小。

的确，关于这个问题已经提出了一些问题[3]。

在这里我还要提到，Llama-index 有自己的[SentenceWindowNodeParser](https://docs.llamaindex.ai/en/stable/api_reference/node_parsers/sentence_window/) [4]，它通常具有相同的缺点。

接下来，我将介绍另一种方法，以实现这个有用的功能，并解决上述提到的两个缺点。在这种方法中，我们将仅使用已经在使用的向量存储。

## 替代实现

精确地说，我们将使用支持仅执行元数据查询选项的向量存储，而不涉及任何相似性搜索。在这里，我将为**ChromaDB**和**Milvus**呈现一个实现示例。这个概念可以轻松适配任何具备此功能的向量数据库。在本教程的最后，我将以**Pinecone**为例进行说明。

**一般概念**

这个概念很简单：

1.  **构建：**在每个块旁边，保存其元数据中生成该块的*document_id*，以及该块的*sequence_number*。

1.  **检索：** 在执行常规的相似性搜索之后（为了简单起见，假设只返回前 1 个结果），我们从检索到的块的元数据中获取 *document_id* 和 *sequence_number*。然后，检索所有具有相同 *document_id* 的周围序列号的块。

例如，假设你已经将一个名为 ***example.pdf*** 的文档分成 80 个块进行索引。那么，对于某个查询，你会发现最接近的向量是具有以下元数据的那个：

```py
{document_id: "example.pdf", sequence_number: 20}
```

你可以轻松地获取来自同一文档、序列号从 15 到 25 的所有向量。

让我们看看代码。

这里，我使用的是：

```py
chromadb==0.4.24
langchain==0.2.8
pymilvus==2.4.4
langchain-community==0.2.7
langchain-milvus==0.1.2
```

下面唯一值得注意的是与每个块相关的元数据，这将帮助我们执行搜索。

```py
from langchain_community.document_loaders import PyPDFLoader
from langchain_core.documents import Document
from langchain_text_splitters import RecursiveCharacterTextSplitter

document_id = "example.pdf"

def preprocess_file(file_path: str) -> list[Document]:
    """Load pdf file, chunk and build appropriate metadata"""
    loader = PyPDFLoader(file_path=file_path)
    pdf_docs = loader.load()

    text_splitter = RecursiveCharacterTextSplitter(
        chunk_size=1000,
        chunk_overlap=0,
    )

    docs = text_splitter.split_documents(documents=pdf_docs)
    chunks_metadata = [
        {"document_id": file_path, "sequence_number": i} for i, _ in enumerate(docs)
    ]
    for chunk, metadata in zip(docs, chunks_metadata):
        chunk.metadata = metadata

    return docs
```

现在，让我们在 Milvus 和 Chroma 中实现实际的检索。请注意，我将使用 LangChains 的对象，而不是原生客户端。这样做是因为我假设开发人员可能希望保留 LangChains 的有用抽象层。另一方面，这将要求我们做一些小的修改，以数据库特定的方式绕过这些抽象层，因此你应该考虑这一点。无论如何，概念保持不变。

再次假设为了简化，我们只需要最相似的向量（“前 1 个”）。接下来，我们将提取相关的 document_id 及其序列号。这将帮助我们检索周围的窗口。

```py
from langchain_community.vectorstores import Milvus, Chroma
from langchain_community.embeddings import DeterministicFakeEmbedding

embedding = DeterministicFakeEmbedding(size=384) # Just for the demo :)

def parent_document_retrieval(
    query: str, client: Milvus | Chroma, window_size: int = 4
):
    top_1 = client.similarity_search(query=query, k=1)[0]
    doc_id = top_1.metadata["document_id"]
    seq_num = top_1.metadata["sequence_number"]
    ids_window = [seq_num + i for i in range(-window_size, window_size, 1)]
    # ...
```

现在，对于窗口/父级检索，我们将在 Langchain 的抽象层下，采用数据库特定的方式进行操作。

对于 Milvus：

```py
 if isinstance(client, Milvus):
        expr = f"document_id LIKE '{doc_id}' && sequence_number in {ids_window}"
        res = client.col.query(
            expr=expr, output_fields=["sequence_number", "text"], limit=len(ids_window)
        )  # This is Milvus specific
        docs_to_return = [
            Document(
                page_content=d["text"],
                metadata={
                    "sequence_number": d["sequence_number"],
                    "document_id": doc_id,
                },
            )
            for d in res
        ]
    # ...
```

对于 Chroma：

```py
 elif isinstance(client, Chroma):
        expr = {
            "$and": [
                {"document_id": {"$eq": doc_id}},
                {"sequence_number": {"$gte": ids_window[0]}},
                {"sequence_number": {"$lte": ids_window[-1]}},
            ]
        }
        res = client.get(where=expr)  # This is Chroma specific
        texts, metadatas = res["documents"], res["metadatas"]
        docs_to_return = [
            Document(
                page_content=t,
                metadata={
                    "sequence_number": m["sequence_number"],
                    "document_id": doc_id,
                },
            )
            for t, m in zip(texts, metadatas)
        ]
```

别忘了按序列号排序：

```py
 docs_to_return.sort(key=lambda x: x.metadata["sequence_number"])
    return docs_to_return
```

为了方便起见，你可以在[这里](https://gist.github.com/omriel1/7243ce233eb2986ed2749de6ae79ecb7)查看完整代码。

## Pinecone（及其他）

据我所知，Pinecone 没有原生方法来执行此类元数据查询，但你可以通过它们的 ID 原生地获取向量（[https://docs.pinecone.io/guides/data/fetch-data](https://docs.pinecone.io/guides/data/fetch-data)）。

因此，我们可以做以下操作：每个块将获得一个唯一 ID，该 ID 本质上是文档 ID 和序列号的连接。然后，给定通过相似性搜索检索到的向量，你可以动态创建周围块的 ID 列表，从而实现相同的结果。

# 限制

值得一提的是，向量数据库并非为了执行“常规”数据库操作而设计，通常也没有针对这类操作进行优化，并且每个数据库的表现都不相同。例如，Milvus 将支持在标量字段（“元数据”）上建立索引，从而优化这类查询。

同时，请注意，这需要向向量数据库发送额外的查询。首先我们检索到最相似的向量，然后我们执行额外的查询，以获取原始文档中周围的块。

当然，正如上面的代码示例所示，实现是特定于向量数据库的，并且 LangChains 的抽象层本身不支持这种实现。

# 结论

在这篇博客中，我们介绍了一种实现方式，用于实现句子窗口检索，这是一种在许多RAG应用中都非常有用的检索技术。在这个实现中，我们仅使用了已经在使用中的向量数据库，并且还支持动态修改检索到的周围窗口大小的选项。

# 参考文献

[1] ARAGOG: 高级RAG输出评分，[https://arxiv.org/pdf/2404.01037](https://arxiv.org/pdf/2404.01037)，第4.2.2节

[2] [https://python.langchain.com/v0.1/docs/modules/data_connection/retrievers/parent_document_retriever/](https://python.langchain.com/v0.1/docs/modules/data_connection/retrievers/parent_document_retriever/)

[3] 一些相关问题：

- [https://github.com/langchain-ai/langchain/issues/14267](https://github.com/langchain-ai/langchain/issues/14267)

- [https://github.com/langchain-ai/langchain/issues/20315](https://github.com/langchain-ai/langchain/issues/20315)

- [https://stackoverflow.com/questions/77385587/persist-parentdocumentretriever-of-langchain](https://stackoverflow.com/questions/77385587/persist-parentdocumentretriever-of-langchain)

[4] [https://docs.llamaindex.ai/en/stable/api_reference/node_parsers/sentence_window/](https://docs.llamaindex.ai/en/stable/api_reference/node_parsers/sentence_window/)
