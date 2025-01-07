# 精通RAG系统：从基础到高级，通过战略性组件评估

> 原文：[https://towardsdatascience.com/mastering-rag-systems-from-fundamentals-to-advanced-with-strategic-component-evaluation-3551be31858f?source=collection_archive---------3-----------------------#2024-04-09](https://towardsdatascience.com/mastering-rag-systems-from-fundamentals-to-advanced-with-strategic-component-evaluation-3551be31858f?source=collection_archive---------3-----------------------#2024-04-09)

## 提升你的RAG系统：通过LLM评估进行高级增强的逐步指南，附带实际数据使用案例

[](https://medium.com/@hamzagharbi_19502?source=post_page---byline--3551be31858f--------------------------------)[![Hamza Gharbi](../Images/da96d29dfde486875d9a4ed932879aef.png)](https://medium.com/@hamzagharbi_19502?source=post_page---byline--3551be31858f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3551be31858f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3551be31858f--------------------------------) [Hamza Gharbi](https://medium.com/@hamzagharbi_19502?source=post_page---byline--3551be31858f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3551be31858f--------------------------------) ·29分钟阅读·2024年4月9日

--

![](../Images/7a0272c5930ac85a330d3af9c48fb8c1.png)

由DALL-E生成的图像。

本文将引导你通过使用`llama-index`框架构建一个先进的检索增强生成（RAG）管道。

检索增强生成（RAG）系统是一个框架，它通过使用外部来源的信息，使生成性AI模型更准确、更可靠。在本项目中，法律文档将作为外部知识库使用。

在本教程中，我们将从建立一个基本的RAG系统开始，然后再说明如何加入高级特性。构建此类系统的挑战之一是决定管道中最佳的组件。我们将通过评估管道中的关键组件来尝试回答这个问题。

本文作为实现RAG系统的实用教程，包括它们的评估。尽管它没有深入探讨理论方面的内容，但它会尽可能详细地解释文章中使用的概念。

# 材料表

· [概述](#690f)

· [设置](#6324)

∘ [1- 代码](#e9f3)

∘ [2- 数据](#83e0)

∘ [3- 原始数据转换](#ab50)

· [基础RAG系统](#6587)

∘ [1- 数据摄取](#0e34)

∘ [2- 索引和存储](#64f9)

∘ [3- 查询](#a526)

∘ [4- 评估](#dd2b)

· [评估嵌入](#5e0e)

· [评估高级特性](#d423)

∘ [1- 窗口化](#f5f5)

∘ [2- 混合搜索](#6ee9)

∘ [3- 查询重写](#2b7c)

· [路由](#f638)

· [结论](#24c9)

· [参考资料](#7830)

· [联系](#3cdf)

# 概述

本文按以下步骤展开：

1- 我们将从使用法国《民法典》数据构建一个**基础的** RAG 系统开始。

2- 接下来，我们将比较 OpenAI、Mistral 和开源模型的各种**嵌入**方法，评估它们的上下文相关性。

3- 我们将探讨像**窗口化**、**混合搜索**和**查询重写**等高级概念，以改进 RAG 系统。这些技术的有效性将通过一组评估查询进行评估。

4- 最后，我们将索引更多的法律条文，并演示如何使用 llama-index 的**路由**功能来选择适当的索引并获得正确的响应。

# 设置

## **1- 代码**

要访问本项目的代码，请执行以下命令，从 GitHub 克隆相应的仓库：

```py
git clone git@github.com:HamzaG737/legal-code-rag.git
```

接下来，你需要安装必要的包。我们使用了 [poetry](https://python-poetry.org/) 作为我们的包管理器，以更好地处理项目依赖。使用以下命令安装 poetry。

```py
curl -sSL https://install.python-poetry.org | python3 -
```

你可以在 [这里](https://python-poetry.org/docs/#installing-with-the-official-installer)了解更多关于 poetry 安装的信息。

然后，在项目根目录下，使用以下命令安装 Python 包：

```py
poetry install
```

对于本项目，要求运行的 Python 环境版本在 3.9 至 3.11 之间。我们强烈建议创建虚拟环境来隔离项目的包依赖，确保它们不会与系统中全局安装的包发生冲突。

本项目还需要**Docker**。

最后，确保定义了 `OPENAI_API_KEY` 环境变量，因为我们将在本项目中使用 OpenAI 的 `gpt-3.5-turbo` LLM 和 Ada 嵌入。如果你有兴趣使用 Mistral 嵌入进行实验，你必须从 [Mistral 平台](https://console.mistral.ai/) 获取 API 密钥。然后，你需要创建相应的环境变量 `MISTRAL_API_KEY`。

## 2- 数据

我们 RAG 的知识库由法国法律条文中的示例组成。法律条文是一个全面的立法文件，旨在权威且逻辑地阐明某一特定法律领域的原则和规则。

例如，**民法典**旨在改革和编纂与私人或民事法相关的法国法律。包括财产、合同、家庭法和个人身份等领域。

一般来说，法律条文是一组有序的条款，通常与某些元数据（如章节、标题、节等）相关联……

目前，法国大约有 78 部有效的法律代码。法国政府通过一个名为 [Légifrance](https://www.legifrance.gouv.fr/) 的网站免费发布这些法律代码。

对于本项目，你有两种方法可以创建知识库：

+   从Legifrance API加载最新数据，该API具有[开放数据状态](https://www.legifrance.gouv.fr/contenu/pied-de-page/open-data-et-api)。你可以在[这里](https://github.com/HamzaG737/legal-code-rag/tree/main?tab=readme-ov-file#get-legifrance-api-keys)找到创建API密钥的说明。然后，我们将使用Python库[pylegifrance](https://github.com/rdassignies/pylegifrance)从API请求特定的法律条文。

+   从`./data/legifrance/{code_name}.json`加载处理后的数据。

由于法律条文频繁变化，我们建议直接从API加载数据，如果你希望获取最新版本的法律条文。然而，创建API密钥可能有些繁琐。如果你时间紧迫且不需要最新内容，可以选择本地加载数据，这是默认设置。

如果你有API密钥并且希望重新加载数据，可以在创建查询引擎时将`reload_data`参数设置为`True`。该引擎代表我们的端到端RAG管道（稍后我们会解释查询引擎的概念）。

## 3- 原始数据转换

本节面向那些有兴趣了解我们如何从Legifrance API转换数据的读者。

在`./data_ingestion/preprocess_legifrance_data.py`中，我们使用以下步骤对来自API的数据进行预处理：

+   我们从API请求某个法律条文的内容。

+   我们递归地从API响应的JSON中检索文章内容。

+   我们去重文章并进行一些清理，如去除部分HTML标签、修剪文本等……

我们在此过程中最终获得的数据是一系列文章，其中每篇文章由其内容、元数据（例如标题、章节、段落等）以及其编号表示。例如：

```py
{'content': "Ni le propriétaire, ni l'usufruitier, ne sont tenus de rebâtir ce qui est tombé de vétusté, ou ce qui a été détruit par cas fortuit.",
 'num': '607',
 'livre': 'Des biens et des différentes modifications de la propriété',
 'titre': "De l'usufruit, de l'usage et de l'habitation",
 'chapitre': "De l'usufruit",
 'section': "Des obligations de l'usufruitier"}
```

这是英文翻译：

```py
{
  "content": "Neither the owner nor the usufructuary are required to rebuild what has fallen into disrepair or what has been destroyed by an act of chance.",
  "num": "607",
  "book": "Of Property and the Various Modifications of Ownership",
  "title": "Of Usufruct, Use and Habitation",
  "chapter": "Of Usufruct",
  "section": "The Obligations of the Usufructuary"
}
```

一篇文章将作为数据的基本单元，并将封装在我们称之为`节点`的结构中。更多内容将在下一节讨论。

# 基本的RAG系统

一个基本的RAG系统包含**四个**重要步骤。我们将逐一考察这些步骤，并说明它们在我们项目中的应用。

![](../Images/dd2c73bcf52db6d8e915faa4f1fe0a54.png)

法律条文上应用的基本RAG系统示意图。图片由作者使用[**Diagrams: Show Me GPT**](https://helpful.dev/)生成。

## 1- 数据摄取

此阶段涉及从各种来源（如PDF文件、数据库、API、网站等）收集和预处理相关数据。此阶段与两个关键概念密切相关：**文档**和**节点**。

在LlamaIndex的术语中，`文档`是指封装任何数据源的容器，如PDF文件、API输出或从数据库中检索的数据。而`节点`则是LlamaIndex中数据的基本单元，代表文档源的一个“块”。节点携带元数据，将它们与所属的文档以及其他节点关联起来。

在我们的项目中，文档将是完整的《民法典》文本，而节点则是该法典的一条条文。然而，由于我们通过 API 已经获得了按条文划分的不同文章数据，因此不需要对文档进行分块，从而避免了由此过程引起的所有错误。

创建节点的代码如下：

```py
from dataclasses import dataclass
from typing import List
import os
from uuid import uuid4
from llama_index.core.schema import TextNode

from loguru import logger

from utils import load_json
from .preprocess_legifrance_data import get_code_articles
from .constants import path_dir_data

@dataclass
class CodeNodes:
    code_name: str
    max_words_per_node: int = 4000
    _n_truncated_articles: int = 0
    ...

    def __post_init__(self):
        self.articles = self.try_load_data()
        self.nodes = self.create_nodes(self.articles)
        ...

    def create_nodes(self, list_articles: List[dict]):
        nodes = [
            TextNode(
                text=article["content"],
                id_=str(uuid4()),
                metadata=self._parse_metadata(article),
            )
            for article in list_articles
        ]

        return nodes

    def try_load_data(self) -> List[dict]:
        path = os.path.join(path_dir_data, f"{self.code_name}.json")
        try:
            code_articles = load_json(path=path)
        except FileNotFoundError:
            logger.warning(
                f"File not found at path {path}. Fetching data from Legifrance."
            )
            code_articles = get_code_articles(code_name=self.code_name)
        truncated_articles = self._chunk_long_articles(code_articles)
        return truncated_articles

    def _parse_metadata(self, article: dict) -> dict:
        metadata = {k: v for k, v in article.items() if k not in ["content", "num"]}
        metadata = {
            "Nom du code": self.code_name,
            **metadata,
            "Article numero": article["num"],
        }
        return metadata

    def _chunk_long_articles(self, articles: List[dict]) -> List[dict]:
        ...
```

+   首先，我们加载 `try_load_data` 中的文章，如前一节所述。

+   我们将长文章分割成“子文章”，以便我们能够无截断地嵌入它们。这是在 `_chunk_long_articles` 中完成的。分块的完整代码可以在`data_ingestion/nodes_processing.py`中找到。

+   我们使用 llama-index 的 `TextNode` 类创建节点。在这一步结束时，我们的数据由一个节点列表组成，每个节点都有文本、ID 和其元数据。例如：

```py
TextNode(
  id_='c0ee164f-ae58-47c8-8e5b-e82f55ac98a9',
  embedding=None,
  metadata={
    'Nom du code': 'Code civil',
    'livre': 'Des différentes manières dont on acquiert la propriété',
    'titre': 'Des libéralités',
    'chapitre': 'Des dispositions testamentaires.',
    'section': 'Du legs universel.',
    'id': '1003',
    'Article numero': '1003'
  },
  excluded_embed_metadata_keys=[],
  excluded_embed_metadata_keys=[],
  relationships={},
  text="Le legs universel est la disposition testamentaire par laquelle le testateur donne à une ou plusieurs personnes l'universalité des biens qu'il laissera à son décès.",
  start_char_idx=None,
  end_char_idx=None,
  text_template='{metadata_str}\n\n{content}',
  metadata_template='{key}: {value}',
  metadata_seperator='\n'
) 
```

元数据将用于嵌入和 LLM 上下文。具体来说，我们将把元数据作为键值对嵌入，并与内容连接，遵循 `text_template` 格式。此格式也将用于在形成 LLM 上下文时表示节点。如果需要，您可以通过更改 `excluded_embed_metadata_keys` 和/或 `excluded_embed_metadata_keys` 来排除部分或全部元数据，用于嵌入或 LLM。

## 2- 索引与存储

**索引**是创建一种数据结构的过程，该结构能够支持数据查询。通常这包括生成`向量嵌入`，这些嵌入是数值表示，能够捕捉数据的核心本质。此外，可以采用多种元数据策略，以有效地检索与上下文相关的信息。嵌入模型不仅用于在索引构建阶段嵌入文档，还用于嵌入我们将来使用查询引擎进行的任何查询。

**存储**是后续阶段，其中创建的索引和其他元数据将被保存，以防止重新索引的需要。这确保了一旦数据被组织和索引，它就可以访问并检索，而无需重新进行索引过程。

`向量嵌入`可以存储并持久化在一个 `向量数据库` 中。在下一节中，我们将看到如何在本地创建和运行这个数据库。

**a- 设置向量数据库**

[Qdrant](https://qdrant.tech/) 将作为向量数据库，用于存储和索引文章的嵌入及其元数据。我们将使用 Qdrant 官方的 Docker 镜像在本地运行服务器。

首先，你可以从 Dockerhub 拉取镜像：

```py
docker pull qdrant/qdrant
```

随后，运行以下命令启动 Qdrant 服务。这也将映射必要的端口，并指定一个本地目录（`./qdrant_storage`）用于数据存储：

```py
docker run -p 6333:6333 -p 6334:6334 \
    -v $(pwd)/qdrant_storage:/qdrant/storage:z \
    qdrant/qdrant
```

设置完成后，我们可以使用 Python 客户端与 Qdrant 服务进行交互：

```py
from qdrant_client import QdrantClient

client = QdrantClient("localhost", port=6333)
```

**b- 索引与存储数据**

以下是用于索引和存储数据的代码。

```py
from llama_index.core import VectorStoreIndex
from llama_index.core import StorageContext
from llama_index.embeddings.mistralai import MistralAIEmbedding
from llama_index.embeddings.openai import OpenAIEmbedding
from llama_index.embeddings.fastembed import FastEmbedEmbedding
from llama_index.vector_stores.qdrant import QdrantVectorStore
from qdrant_client import QdrantClient
from qdrant_client.http.exceptions import UnexpectedResponse
from loguru import logger

from data_ingestion.nodes_processing import CodeNodes

def index_given_nodes(
    code_nodes: CodeNodes,
    embedding_model: MistralAIEmbedding | OpenAIEmbedding | FastEmbedEmbedding,
    hybrid_search: bool,
    recreate_collection: bool = False,
) -> VectorStoreIndex:
    """
    Given a list of nodes, create a new index or use an existing one. 

    Parameters
    ----------
    code_nodes : CodeNodes
        The nodes to index.
    embedding_model : MistralAIEmbedding | OpenAIEmbedding | FastEmbedEmbedding
        The embedding model to use.
    hybrid_search : bool
        Whether to enable hybrid search.
    recreate_collection : bool, optional
        Whether to recreate the collection, by default False.

    """

    collection_name = code_nodes.nodes_config
    client = QdrantClient("localhost", port=6333)
    if recreate_collection:
        client.delete_collection(collection_name)

    try:
        count = client.count(collection_name).count
    except UnexpectedResponse:
        count = 0

    if count == len(code_nodes.nodes):
        logger.info(f"Found {count} existing nodes. Using the existing collection.")
        vector_store = QdrantVectorStore(
            collection_name=collection_name,
            client=client,
            enable_hybrid=hybrid_search,
        )
        return VectorStoreIndex.from_vector_store(
            vector_store,
            embed_model=embedding_model,
        )
    logger.info(
        f"Found {count} existing nodes. Creating a new index with {len(code_nodes.nodes)} nodes. This may take a while."
    )
    if count > 0:
        client.delete_collection(collection_name)

    vector_store = QdrantVectorStore(
        collection_name=collection_name,
        client=client,
        enable_hybrid=hybrid_search,
    )
    storage_context = StorageContext.from_defaults(vector_store=vector_store)
    index = VectorStoreIndex(
        code_nodes.nodes,
        storage_context=storage_context,
        embed_model=embedding_model,
    )
    return index
```

索引函数的逻辑非常简单：

+   首先，我们创建 Qdrant 客户端来与向量数据库进行交互。

+   我们检查所需集合中的节点数量。该集合名称由`code_nodes.nodes_config`提供，代表当前实验（即代码名称、嵌入方法以及可能的高级RAG技术，如*base, window-nodes, hybrid search…* 更多关于这些实验的内容稍后介绍）。如果节点数量与当前数量不同，我们将创建一个新索引：

```py
storage_context = StorageContext.from_defaults(vector_store=vector_store)
index = VectorStoreIndex(
        code_nodes.nodes,
        storage_context=storage_context,
        embed_model=embedding_model,
    )
```

否则我们返回现有的索引：

```py
index = VectorStoreIndex.from_vector_store(
            vector_store,
            embed_model=embedding_model,
        )
```

创建或检索索引的完整代码可以在`./retriever/get_retriever.py`模块中找到。你还可以在 llama-index [文档](https://docs.llamaindex.ai/en/stable/understanding/storing/storing/)中找到更多关于此阶段的信息。

## 3- 查询

给定用户查询，我们计算查询嵌入与索引节点嵌入之间的相似度，以找到最相似的数据。这些相似节点的内容随后被用来生成上下文。这个上下文使得语言模型能够为用户合成一个响应。

我们基于上一节创建的索引定义一个`查询引擎`。这个引擎是一个端到端管道，接收自然语言查询并返回响应，以及检索到并传递给LLM的上下文。

```py
from llama_index.core.query_engine import BaseQueryEngine
from llama_index.core import VectorStoreIndex
from llama_index.core import PromptTemplate

def get_query_engine_based_on_index(
    index: VectorStoreIndex,
    similarity_top_k: int = 5,
) -> BaseQueryEngine:

    query_engine = index.as_query_engine(similarity_top_k=similarity_top_k)

    query_engine = update_prompts_for_query_engine(query_engine)
    return query_engine
```

`update_prompts_for_query_engine`是一个函数，允许我们更改响应合成器的提示，即负责将上下文作为输入并为用户生成答案的LLM。

```py
def update_prompts_for_query_engine(query_engine: BaseQueryEngine) -> BaseQueryEngine:

    new_tmpl_str = (
        "Below is the specific context required to answer the upcoming query. You must base your response solely on this context, strictly avoiding the use of external knowledge or assumptions..\n"
        "---------------------\n"
        "{context_str}\n"
        "---------------------\n"
        "Given this context, please formulate your response to the following query. It is imperative that your answer explicitly mentions any relevant code name and article number by using the format 'according to code X and article Y,...'. Ensure your response adheres to these instructions to maintain accuracy and relevance."
        "Furthermore, it is crucial to respond in the same language in which the query is presented. This requirement is to ensure the response is directly applicable and understandable in the context of the query provided."
        "Query: {query_str}\n"
        "Answer: "
    )
    new_tmpl = PromptTemplate(new_tmpl_str)
    query_engine.update_prompts({"response_synthesizer:text_qa_template": new_tmpl})
    return query_engine
```

在这个新模板中，我们强调防止幻觉的必要性。我们还指示LLM在提供响应之前始终参考代码名称，如民法典，以及条文号。此外，我们指示它用查询的语言进行回复，以支持多语言查询。

`create_query_engine`函数在`query_engine`模块中是创建RAG管道的入口。此函数的参数定义了RAG配置参数。

以下代码创建一个基本的查询引擎，并根据特定查询生成响应：

```py
from query.query_engine import create_query_engine

query_engine = create_query_engine()

response = query_engine.query("What are the conditions required for a marriage to be considered valid ?")

print(response)
```

你可以在`./query/query_engine.py`中找到创建查询引擎的完整代码。更多关于llama-index查询引擎的信息可以在[这里](https://docs.llamaindex.ai/en/stable/module_guides/deploying/query_engine/)找到。

## 4- 评估

[评估](https://docs.llamaindex.ai/en/stable/understanding/evaluating/evaluating/)对于评估RAG管道的性能至关重要，并验证关于其组件所做的决策。

在这个项目中，我们将采用基于LLM的评估方法来评估结果的质量。这项评估将涉及一个金标准LLM，按照一定的指标分析一组响应，以确保RAG管道的有效性和准确性。

通常，RAG系统可以通过两种方式进行评估：

+   **响应评估**：响应是否与检索到的上下文和查询一致？

+   **检索评估**：检索到的数据源是否与查询相关？

`LLama-index`提供了几种评估器来计算不同的指标，如**忠实度**、**上下文相关性**和**回答相关性**。您可以在评估部分找到有关这些指标的更多细节。

这是在此项目中创建评估器的过程：

+   最初，我们使用LLM（在我们这个案例中是gpt-4）生成`n`个问题，关于我们想要评估的法律代码。出于成本考虑，我们设置了`n=50`，但更大的问题数量可能会提供对系统性能更有信心的评估。一个LLM生成的问题示例是：“法国重婚的法律后果是什么？”或者法语版本：“Quelles sont les conséquences juridiques de la bigamie en France ?”。请注意，这种方法有其局限性，因为LLM生成的问题可能无法反映真实用户提问的实际分布。您可以在`evaluation/generate_questions.py`找到问题生成模块。

+   使用生成的问题列表、评估指标和查询引擎，我们生成响应列表。每个响应由评估器在0到1之间打分，评估器还提供反馈文本来解释其评分。

+   `llama-index`提供了不同指标的评估器，例如`ContextRelevancyEvaluator`用于计算上下文相关性。此外，我们重写了这些评估器的`evaluate_response`方法，以在嵌入和创建上下文时考虑元数据。

```py
from typing import Any, Optional, Sequence

from llama_index.core.evaluation import ContextRelevancyEvaluator, EvaluationResult
from llama_index.core.response import Response
from llama_index.core.schema import MetadataMode

class CustomContextRelevancyEvaluator(ContextRelevancyEvaluator):
    def __init__(self, **kwargs):
        super().__init__(**kwargs)

    def evaluate_response(
        self,
        query: Optional[str] = None,
        response: Optional[Response] = None,
        metadata_mode: MetadataMode = MetadataMode.ALL,
        **kwargs: Any,
    ) -> EvaluationResult:
        """Run evaluation with query string and generated Response object.

        Subclasses can override this method to provide custom evaluation logic and
        take in additional arguments.
        """
        response_str: Optional[str] = None
        contexts: Optional[Sequence[str]] = None
        if response is not None:
            response_str = response.response
            contexts = [
                node.get_content(metadata_mode=metadata_mode)
                for node in response.source_nodes
            ]

        return self.evaluate(
            query=query, response=response_str, contexts=contexts, **kwargs
        )
```

+   以下是从特定评估器（例如上下文相关性评估器）获取评估结果的代码。值得注意的是，您可以以异步模式生成这些评估，以便更快地获得结果。然而，由于OpenAI的速率限制，我们是按顺序执行这些评估的。

```py
from typing import Literal, List

from llama_index.core.query_engine import BaseQueryEngine
from llama_index.llms.openai import OpenAI
from llama_index.core.evaluation import EvaluationResult
from llama_index.core.schema import MetadataMode
from tqdm import tqdm

from utils import load_json
from evaluation.custom_evaluators import CustomContextRelevancyEvaluator

PATH_EVAL_CODE_CIVIL = "./data/questions_code_civil.json"
PATH_EVAL_CODE_DE_LA_ROUTE = "./data/questions_code_de_la_route.json"

def evaluate_one_metric(
    query_engine: BaseQueryEngine,
    code_name: Literal["code_civil", "code_de_la_route"],
    metadata_mode: MetadataMode,
    llm_for_eval: str = "gpt-3.5-turbo",
) -> List[EvaluationResult]:

    evaluator = CustomContextRelevancyEvaluator(
        llm=OpenAI(temperature=0, model=llm_for_eval)
    )

    eval_data: List[str] = load_json(globals()[f"PATH_EVAL_{code_name.upper()}"])

    results = []
    for question in tqdm(eval_data):
        response = query_engine.query(question)
        eval_result = evaluator.evaluate_response(
            query=question, response=response, metadata_mode=metadata_mode
        )

        results.append(eval_result)

    return results
```

+   最后，为了获取某个管道（由其查询引擎定义）的得分，我们计算评估器给每个查询的分数的平均值。

评估的完整代码位于`evaluation/eval_with_llamaindex.py`。

对于即将进行的RAG管道评估，我们将使用由GPT-4基于法国民法典生成的50个问题。这些问题可以在`./data/questions_code_civil.json`找到。用于评估的金标准LLM是`gpt-3.5-turbo`。

# 评估嵌入

**所有的评估实验可以在这个** [**notebook**](https://github.com/HamzaG737/legal-code-rag/blob/main/notebooks/evaluate_with_llamaindex.ipynb) **中找到。**

我们将首先评估民法典条文（或节点）的嵌入。为了选择我们将要评估的嵌入模型集，我们依赖于Huggingface的[排行榜](https://huggingface.co/spaces/mteb/leaderboard)，用于在法语数据上的检索。因此，我们将评估这三种模型：

+   **Text-Embedding-Ada-002**来自OpenAI，是目前（撰写本文时）在此任务中最先进的技术。

+   **mistral-embed**来自Mistral AI，紧随其后。

+   **multilingual-e5-large**来自Infloat，作为开源候选者。我们还将使用Qdrant的[**fast-embed**](https://qdrant.tech/articles/fastembed/)框架来提高嵌入创建的效率，主要通过量化权重并使用ONNX作为运行时。

我们可以通过导入相应的集成来使用llama-index生成这些嵌入。例如，我们导入`FastEmbedEmbedding`模块，使用`fast-embed`框架生成`e5-large`嵌入：

```py
from llama_index.embeddings.fastembed import FastEmbedEmbedding

embeddings_model_name = "intfloat/multilingual-e5-large"
embed_model = FastEmbedEmbedding(model_name=embeddings_model_name)
```

完整的嵌入定义可以在`retriever/embeddings.py`中找到。

我们将衡量的指标是上下文相关性，即检索到的上下文与用户查询的相关性。

这是启动本次实验评估的代码。

```py
from query.query_engine import create_query_engine
from evaluation.eval_with_llamaindex import evaluate_multiple_experiments

query_engine_fastembed = create_query_engine(embedding_model="intfloat/multilingual-e5-large")
query_engine_mistral = create_query_engine(embedding_model="mistral-embed")
query_engine_ada = create_query_engine(embedding_model="text-embedding-ada-002")

exp_to_query_engine = {
    "mistral_embed": query_engine_mistral,
    "fastembed": query_engine_fastembed,
    "ada": query_engine_ada,
}

scores_df, deeps_df = evaluate_multiple_experiments(
    experiment_to_query_engine=exp_to_query_engine,
    general_exp_name="embeddings",
    list_metrics=["context_relevancy"],
    code_name="code_civil"
)
```

现在这是最终的结果表格。`embedding_time`字段表示我们将约`2800`篇《民法典》文章进行嵌入所花费的时间。

我们观察到，`text-embedding-data-002`在得分和嵌入时间上都优于其他两个嵌入模型。`mistral-embed`和`ada`与`multilingual-e5-large`之间的差距相当显著。请注意，这些嵌入方法的排名与之前提到的排行榜一致。

然而，请记住，我们的评估仅基于由LLM生成的50个问题。因此，它可能无法完全代表真实场景中的表现，尤其是在区分`mistral-embed`和`ada`时，因为这两者之间的差距相对较小。

# 评估高级功能

## 1- 窗口化

我们将探索的第一个高级功能是通过前后相邻节点的内容来增强节点内容。在法律领域中，这种做法是有意义的，因为相邻的条款通常紧密相关。

我们将使用`k`来参数化窗口化，表示当前节点前后需要添加的节点数量。例如，`k=1`意味着当前节点的内容将与前后节点一起增强。

对于给定的节点，将使用原始内容进行嵌入，而增强后的内容将用于生成响应的上下文。

下面是我们如何进行节点窗口化的：

+   我们创建了一个自定义函数来生成增强后的节点。尽管Llama-index有其`SentenceWindowNodeParser`类来执行此任务，但它要求输入的是完整文档，然后进行分割以创建节点。因此，我们从这个类获得灵感，创建了自己的窗口解析器。

```py
from typing import List
from llama_index.core.schema import TextNode
from tqdm import tqdm

from .constants import possible_headers

WINDOW_METADATA_KEY = "window"
ORIGINAL_TEXT_METADATA_KEY = "original_text"

def add_window_nodes(nodes: List[TextNode], window_size: int = 3):
    for i, node in tqdm(
        enumerate(nodes), total=len(nodes), desc="Adding window nodes ..."
    ):
        window_nodes = nodes[
            max(0, i - window_size) : min(i + window_size + 1, len(nodes))
        ]

        node.metadata[WINDOW_METADATA_KEY] = "\n".join(
            [n.get_content("llm") for n in window_nodes]
        )
        node.metadata[ORIGINAL_TEXT_METADATA_KEY] = node.text

        # exclude window metadata from embed and llm
        node.excluded_embed_metadata_keys.extend(
            [WINDOW_METADATA_KEY, ORIGINAL_TEXT_METADATA_KEY]
        )

        node.excluded_llm_metadata_keys.extend(
            [WINDOW_METADATA_KEY, ORIGINAL_TEXT_METADATA_KEY]
        )

    # since articles metadata (like title, chapter, etc ...) will be incorporated in WINDOW_METADATA_KEY,
    # we can exclude them from the llm metadata.

    for node in nodes:
        node.excluded_llm_metadata_keys.extend(possible_headers)

    return nodes
```

+   然后我们修改之前创建的`CodeNodes`类，增加使用相邻节点进行增强的功能：

```py
from dataclasses import dataclass
from typing import List
from llama_index.core.postprocessor import MetadataReplacementPostProcessor

from loguru import logger

from .window_nodes import add_window_nodes

@dataclass
class CodeNodes:
    code_name: str
    use_window_nodes: bool
    nodes_window_size: int = 3
    max_words_per_node: int = 4000
    _n_truncated_articles: int = 0

    def __post_init__(self):
        self.articles = self.try_load_data()
        self.nodes = self.create_nodes(self.articles)
        code_name_no_spaces = self.code_name.replace(" ", "_")
        self.nodes_config = f"{code_name_no_spaces}_base"
        self.post_processors = []
        if self.use_window_nodes:
            logger.info("Adding window nodes ...")
            self.nodes = add_window_nodes(self.nodes, self.nodes_window_size)
            self.nodes_config = f"{code_name_no_spaces}_window"
            self.post_processors.append(
                MetadataReplacementPostProcessor(target_metadata_key="window")
            )

    def create_nodes(self, list_articles: List[dict]): ...

    def try_load_data(self) -> List[dict]: ...

    def _parse_metadata(self, article: dict) -> dict: ...

    def _chunk_long_articles(self, articles: List[dict]) -> List[dict]: ...
```

+   一个关键点是`MetadataReplacementPostProcessor`。这个类用于指示我们需要在将检索到的数据传递给LLM之前，将节点内容替换为在`target_metadata_key`字段中找到的内容。因此，这个字段将包含增强后的内容。

+   最后，我们需要将后处理器集成到查询引擎中：

```py
from llama_index.core.query_engine import BaseQueryEngine
from llama_index.core import VectorStoreIndex

def get_query_engine_based_on_index(
    index: VectorStoreIndex,
    postprocessors_list: list,
    similarity_top_k: int = 5,
) -> BaseQueryEngine:

    query_engine = index.as_query_engine(
        similarity_top_k=similarity_top_k, node_postprocessors=postprocessors_list
    )

    query_engine = update_prompts_for_query_engine(query_engine)
    return query_engine
```

在此实验中，我们将比较基本 RAG 管道与另外两个管道。这些额外的管道分别使用 `k=1` 和 `k=2` 的节点窗口。我们将使用相同的一组问题和上下文相关性指标来评估这些管道。以下是结果：

我们可以观察到，通过增加邻近节点的内容来增强节点的上下文相关性得分。小幅的提升可能是由于已经很高的得分（0.89）。

我们还将衡量本次实验的**忠实度**。在 llama-index 中，`FaithfulnessEvaluator` 模块用于判断查询引擎的回答是否与任何源节点匹配，从而确定回答是否为幻觉。需要注意的是，该评估器提供一个二元得分（如果回答是幻觉则为 0，否则为 1）。这与**上下文相关性**得分不同，后者的范围是 0 到 1。

以下是结果：

在我们对 50 个问题的评估中，我们发现基本实验中只有一个回答是幻觉，而在使用`k=2`的窗口管道中有五个幻觉。这表明，添加更多上下文，特别是无关上下文，可能会诱发更多幻觉。

在随后的实验中，我们将保持基本设置，不进行节点扩展。这个决定基于我们的观察，即相关性的小幅增加并不足以抵消回答中的更高幻觉率。

## 2- 混合搜索

[混合搜索](https://docs.llamaindex.ai/en/stable/examples/vector_stores/qdrant_hybrid/)是指结合`稀疏`和`稠密`向量的搜索结果。

`稠密向量`将数据点表示为连续值的紧凑向量，提供对数据特征的丰富且微妙的理解。这些向量通常是通过深度学习模型（如 OpenAI、Mistral、e5-large 等）生成的……到目前为止，我们在检索阶段仅使用了稠密向量。

相比之下，`稀疏向量`主要是零，通过诸如 TF-IDF、BM25 等专业模型生成。它们擅长识别特定关键词和细节，使其与更具语义丰富性的稠密向量有所不同。

为了在我们的 RAG 管道中实现混合搜索，我们需要修改代码的某些部分：

+   首先，我们需要通过在 Qdrant 客户端定义中设置`enable_hybrid=True`来启用混合搜索。这将使用 Huggingface 中的 `"naver/efficient-splade-VI-BT-large-doc"` 模型在本地生成稀疏向量，同时使用 OpenAI 的 ada 模型生成常规的稠密向量。

```py
vector_store = QdrantVectorStore(
    collection_name=collection_name,
    client=client,
    enable_hybrid=True,
)
```

+   在定义`query_engine = index.as_query_engine(**kwargs)`时，我们需要同时定义`sparse_top_k`和`similarity_top_k`。`sparse_top_k`表示每个稀疏和密集查询将检索多少个节点。例如，设置`sparse_top_k=5`意味着我们将使用稀疏向量检索5个节点，使用密集向量检索5个节点。`similarity_top_k`控制返回节点的最终数量。在上述设置中，我们最终会得到10个节点。接着，应用一种融合算法来对来自不同向量空间的节点进行排序和排列（此处使用的是[相对评分融合](https://weaviate.io/blog/hybrid-search-fusion-algorithms#relative-score-fusion)）。`similarity_top_k=5`意味着返回融合后的前五个节点。以下是添加这些选项后的`query_engine`的新定义：

```py
query_engine = index.as_query_engine(
    similarity_top_k=5, 
    sparse_top_k=5, 
    vector_store_query_mode="hybrid", 
    **kwargs
)
```

+   我们可以通过调整`alpha`参数来调整向量搜索和关键词搜索的权重。`alpha`等于1表示纯向量搜索，而`alpha`等于0表示纯关键词搜索。以下是结合此`alpha`参数后的`query_engine`最终定义：

```py
query_engine = index.as_query_engine(
    similarity_top_k=5, 
    sparse_top_k=5, 
    alpha=0.5,
    vector_store_query_mode="hybrid", 
    **kwargs
)
```

为了评估混合搜索的相关性，我们进行了三次不同`alpha`参数的实验：`alpha = 0.2, 0.5, 0.8`。请注意，基础实验对应`alpha=1`，表示纯向量搜索。

这是关于上下文相关性和忠实度指标的最终结果。请注意，使用`"naver/efficient-splade-VI-BT-large-doc"`模型嵌入`~2800`个节点大约花费了我们`27分钟`，并且该过程在没有GPU的M1 Mac上进行，几乎占用了所有系统内存。因此，您可能需要GPU来加速嵌入过程。

`alpha=0.8`的混合搜索在上下文相关性评分上略有提升，但将更多权重放在关键词搜索上会降低评分。忠实度评分总体保持不变。

请记住，评估问题相对一般，更偏向语义搜索而非精确匹配搜索。然而，在现实场景中，用户可能会要求查找代码中的特定文章，例如“文章x.y讨论了什么？”在这种情况下，关键词搜索可能会有所帮助。因此，尽管与纯向量搜索相比增加了额外的幻觉案例，我们仍将保留`alpha=0.8`的混合搜索供未来实验使用。

## 3- 查询重写

[查询重写](https://docs.llamaindex.ai/en/stable/examples/retrievers/reciprocal_rerank_fusion/)涉及生成与特定查询相似的各种问题。此过程可用于消歧义、错误修正或将查询适应于支持RAG系统的特定知识库。

我们将使用来自llama-index的`QueryFusionRetriever`进行查询重写。这个模块生成与用户查询相似的查询，从每个生成的查询中检索并重新排序前`n`个节点，包括原始查询，使用`Reciprocal Rerank Fusion`算法。该方法在这篇[论文](https://plg.uwaterloo.ca/~gvcormac/cormacksigir09-rrf.pdf)中有详细介绍，提供了一种高效的方式来重新排序检索到的节点，而不会产生过多的计算或依赖外部模型。

以下是此任务的Python代码（位于`query/query_engine.py`）：

```py
from llama_index.core.query_engine import BaseQueryEngine, RetrieverQueryEngine
from llama_index.core import VectorStoreIndex
from llama_index.core.retrievers import QueryFusionRetriever

from query.constants import QUERY_GEN_PROMPT

def get_query_fusion_retrieval(
    index: VectorStoreIndex,
    postprocessors_list: list,
    similarity_top_k: int = 5,
    sparse_top_k: int = 0,
    hybrid_search_alpha: float = 0.5,
    hybrid_search: bool = False,
    num_generated_questions: int = 4,
) -> BaseQueryEngine:
    kwargs = {"similarity_top_k": similarity_top_k}
    if hybrid_search:
        kwargs.update(
            {
                "vector_store_query_mode": "hybrid",
                "sparse_top_k": sparse_top_k,
                "alpha": hybrid_search_alpha,
            }
        )

    retriever = index.as_retriever(**kwargs)

    retriever = QueryFusionRetriever(
        [retriever],
        similarity_top_k=similarity_top_k,
        num_queries=num_generated_questions,  # set this to 1 to disable query generation
        mode="reciprocal_rerank",
        use_async=False,
        verbose=False,
        query_gen_prompt=QUERY_GEN_PROMPT,
    )

    query_engine = RetrieverQueryEngine.from_args(
        retriever=retriever, node_postprocessors=postprocessors_list
    )
    query_engine = update_prompts_for_query_engine(query_engine)
    return query_engine
```

+   `num_queries`表示总查询数：`num_queries-1`个生成的查询，加上`1`个原始查询。对于后续实验，我们设置`num_queries=4`。

+   `QUERY_GEN_PROMPT`是专门用于生成类似查询的提示。我们为民法典知识库创建了这个提示的自定义版本。

```py
QUERY_GEN_PROMPT = (
    "You are a helpful assistant that generates multiple search queries based on a "
    "single input query. Generate {num_queries} search queries, one on each line, "
    "related to the following input query. The queries must be in French and specifically "
    "adapted to query the French Civil Code, and they must address the ambiguities in the input query:\n"
    "Query: {query}\n"
    "Queries:\n"
)
```

直到现在，我们使用的评估问题已经很干净，并且针对民法典进行了定制，因为我们指示GPT-4按照这种方式生成它们。然而，正如我们之前提到的，这可能无法准确代表现实中的用户，因为他们可能会提出模糊、容易出错或简短的问题。因此，我们使用GPT-4生成了另外`50`个问题，并指示LLM生成具有这些更现实特征的问题。以下是一些例子：

```py
[
    "C'est quoi un contrat de mariage?",
    "On peut changer de prénom facilement?",
    "Qu'est-ce qui se passe si on trouve un trésor chez quelqu'un d'autre?",
    "Comment on fait pour adopter un enfant?",
    "C'est quoi exactement une servitude?",
    "Si je construis un truc chez le voisin sans faire exprès, je dois le démolir?",
]
```

或者是英文翻译：

```py
[
    "What is a marriage contract?",
    "Can we change our first name easily?",
    "What happens if we find a treasure on someone else's property?",
    "How do we adopt a child?",
    "What exactly is an easement?",
    "If I accidentally build something on the neighbor's property, do I have to demolish it?",
]
```

我们将使用这个新的评估数据集来比较带有和不带有查询重写的管道的表现。比较将基于两个指标：**准确性**和**答案相关性**。类似于上下文相关性，答案相关性衡量答案与用户查询的相关性，得分范围从0到1。结果如下：

我们观察到，查询重写不仅提高了准确性，模型在50个问题中完全没有虚构内容，而且稍微提高了答案相关性得分。

下面是一个示例，展示了原始问题如何被重写成另外三个问题：

+   原始问题：`我可以拒绝继承吗？`

+   三个生成的查询：

```py
 1 - What are the legal procedures for refusing an inheritance according to the French Civil Code?
2 - What are the rights and obligations of an heir who wishes to renounce a succession in France?
3 - How does the renunciation of an inheritance proceed under the French Civil Code?
```

# 路由

到目前为止，我们的知识库中每个RAG管道只包含一个合法代码。自然地，人们可能会想，如何添加更多的合法代码，例如刑法或交通法规，以及如何构建一个系统，当给定查询时，能够从正确的法律代码中检索数据。我们可以通过至少三种方式使用llama-index来实现这一目标：

+   将不同法律代码中的所有数据存储在同一个索引中，并像以前一样构建查询引擎。

+   将所有数据存储在同一个索引中，但将法律代码名称作为元数据。在查询时，llama-index的`[Auto-retriever](https://docs.llamaindex.ai/en/stable/examples/vector_stores/chroma_auto_retriever/)`模块推断出一组元数据过滤器和适当的查询字符串，以传递给向量数据库。`auto-retriever`必须确定正确的代码名称，然后在相应的节点上执行相似度搜索以检索数据。

+   将每个法律代码节点存储在单独的索引中，并使用llama-index的`Routing`功能选择最相关的索引或多个索引。

在这个项目中，我们选择了最后一个选项。更有效的方法是评估这三种选择并选出最佳方案。然而，由于时间和空间的限制，我们没有进行这个评估，因为这篇文章已经相当详细。

[路由器](https://docs.llamaindex.ai/en/stable/module_guides/querying/router/)是接受查询和一组**选择**并返回一个或多个选择的模块。

选择过程由LLM执行。在我们的用例中，选择器接收法律代码描述作为输入，并返回一个或多个查询引擎。每个引擎代表一个单一代码的RAG系统。

这是我们在这个项目中进行路由实验的方式：

+   我们首先定义了要考虑的法律代码列表及其对应的描述。我们在路由实验中使用的代码包括民法典、税法典、知识产权法典、交通法规法典和劳动法典。

```py
codes_to_description = {
    "Code civil": "Code civil: code juridique qui regroupe les lois relatives au droit civil français, c’est-à-dire l'ensemble des règles qui déterminent le statut des personnes (livre Ier), celui des biens (livre II) et celui des relations entre les personnes privées (livres III et IV).",
    "Code général des impôts": "Code général des impôts: code juridique qui regroupe les lois relatives aux impôts en France, c’est-à-dire l'ensemble des règles qui déterminent les impôts et les taxes.",
    "Code de la propriété intellectuelle": "Code de la propriété intellectuelle: code juridique qui regroupe les lois relatives à la propriété intellectuelle en France, c’est-à-dire l'ensemble des règles qui déterminent les droits des auteurs, des artistes-interprètes, des producteurs de phonogrammes et de vidéogrammes et des entreprises de communication audiovisuelle.",
    "Code de la route": "Code de la route: code juridique qui regroupe les lois relatives à la circulation routière en France, c’est-à-dire l'ensemble des règles qui déterminent les droits et les devoirs des usagers de la route.",
    "Code du travail": "Code du travail: code juridique qui regroupe les lois relatives au droit du travail en France, c’est-à-dire l'ensemble des règles qui déterminent les droits et les devoirs des employeurs et des salariés.",
}
```

+   我们为每个代码实例化了`query_engine`，如前所述。然后，我们将每个`query_engine`与其描述一起包装在`QueryEngineTool`模块中。

```py
from llama_index.core.tools import QueryEngineTool

from query.query_engine import create_query_engine

def get_tools():
    tools = []
    for code_name, code_description in codes_to_description.items():
        query_engine = create_query_engine(code_name=code_name)
        tool = QueryEngineTool.from_defaults(
            query_engine=query_engine,
            description=code_description,
        )
        tools.append(tool)
    return tools
```

+   最后，我们使用工具列表和llama-index的`LLMMultiSelector`模块创建了路由查询引擎。LLM选择器将选项作为文本转储放入提示中，并使用LLM文本完成端点来做出决策。

```py
from llama_index.core.query_engine import RouterQueryEngine
from llama_index.core.selectors import LLMMultiSelector

def create_routing_engine():
    query_engine_tools = get_tools()
    query_engine = RouterQueryEngine(
        selector=LLMMultiSelector.from_defaults(),
        query_engine_tools=query_engine_tools,
    )
    return query_engine
```

请注意，为了简化，我们没有在单个代码查询引擎中包含任何高级功能。

现在，让我们在包含两个主题的查询上测试路由查询引擎：劳动法和税法。这将使我们看到路由器是否选择了正确的索引。

查询：

```py
Quelles sont les conditions pour bénéficier d'un congé sabbatique \n Comment sont imposées les plus-values immobilières
```

或者它的英文翻译：

```py
What are the conditions to qualify for a sabbatical leave? \n How are capital gains on real estate taxed?
```

现在我们可以检查路由查询引擎给出的响应：

```py
{
  "response": "Pour bénéficier d'un congé sabbatique, le salarié doit justifier d'une ancienneté minimale dans l'entreprise, cumulée sur plusieurs périodes non consécutives, ainsi que de six années d'activité professionnelle. De plus, il ne doit pas avoir bénéficié depuis une durée minimale, dans la même entreprise, d'un congé sabbatique, d'un congé pour création d'entreprise ou d'un congé spécifique d'une durée d'au moins six mois.\n\nLes plus-values provenant de la cession en cours d'exploitation des éléments de l'actif immobilisé et réalisées avant l'entrée en vigueur des dispositions spécifiques ne sont pas comprises dans le bénéfice imposable si le contribuable s'engage à réinvestir dans des immobilisations dans son entreprise dans un délai de trois ans.",
  "source_nodes": [
    {
      ....
      "text": "A défaut de convention ou d'accord mentionné à l'article L. 3142-32, le salarié informe l'employeur de la date de départ en congé sabbatique qu'il a choisie et de la durée de ce congé, par tout moyen conférant date certaine, au moins trois mois à l'avance."
    },
    {
      ....
      "text": "L'employeur informe le salarié de son accord sur la date de départ choisie du congé sabbatique ou de son report par tout moyen conférant date certaine."
    },
    {
      ....
      "text": "A défaut de convention ou d'accord mentionné à l'article L. 3142-32, le départ en congé peut être différé par l'employeur dans les conditions mentionnées au premier alinéa de l'article L. 3142-29, de telle sorte que le pourcentage des salariés simultanément absents de l'entreprise au titre du congé sabbatique ne dépasse pas 1,5 % de l'effectif de cette entreprise, jusqu'à la date à laquelle cette condition de taux est remplie ou que le nombre de jours d'absence au titre du congé sabbatique ne dépasse pas 1,5 % du nombre de jours de travail effectués dans les douze mois précédant le départ en congé. Pour permettre le départ en congé d'un salarié, cette période de douze mois est prolongée dans la limite de quarante-huit mois."
    },
    {
      ....
      "text": "Le salarié a droit à un congé sabbatique pendant lequel son contrat de travail est suspendu.\nLe droit à ce congé est ouvert au salarié justifiant, à la date de départ en congé, d'une ancienneté minimale dans l'entreprise, cumulée, le cas échéant, sur plusieurs périodes non consécutives, ainsi que de six années d'activité professionnelle et n'ayant pas bénéficié depuis une durée minimale, dans la même entreprise, d'un congé sabbatique, d'un congé pour création d'entreprise ou d'un congé spécifique mentionné à l'article L. 6323-17-1 d'une durée d'au moins six mois. L'ancienneté acquise dans toute autre entreprise du même groupe, au sens de l'article L. 2331-1, est prise en compte au titre de l'ancienneté dans l'entreprise."
    }, ...
  ],
  "metadata": {
    "selector_result": {
      "selections": [
        {
          "index": 4,
          "reason": "Le Code du travail regroupe les lois relatives au droit du travail en France, ce qui inclut les conditions pour bénéficier d'un congé sabbatique."
        },
        {
          "index": 1,
          "reason": "Le Code général des impôts regroupe les lois relatives aux impôts en France, ce qui inclut les règles sur l'imposition des plus-values immobilières."
        }
      ]
    }
  }
}
```

这里的重要结果是`selector_result`，它显示多选择器正确地识别了劳动法（索引4）和税法（索引1）作为回答用户查询的相关数据源。

你可以在笔记本`./notebooks/evaluate_with_llamaindex.ipynb`中找到所有的评估实验和路由查询引擎定义。

# 结论

在本文中，我们讨论了如何从一个基础的RAG系统过渡到一个高级系统，采用窗口化、混合搜索和查询重写，并使用`llama-index`框架。我们还探讨了如何评估RAG管道的某些组件，如嵌入和前述功能，从而做出关于其相关性的知情决策，而不是仅凭直觉。

我们还研究了路由如何通过LLM决策使我们能够选择相关的索引。

以下是一些进一步优化管道的建议：

+   在这个项目中，我们使用了`gpt-3.5-turbo`作为LLM来生成响应，因为它易于使用且成本较低。与我们评估RAG系统的各个组件类似，我们也可以根据如答案相关性和准确性等指标来评估LLM。

+   同样的评论适用于将`gpt-3.5-turbo`作为评估器。使用更强大的语言模型可能会提高评估的准确性。

+   理想情况下，我们会使用各种指标评估RAG管道的所有组合。然而，这可能代价高昂，并且可能不可行。

+   也许值得考虑在我们的系统中评估其他先进技术，例如使用外部模型进行重排序、使用代理等。

+   我们还可以增强RAG管道的多语言支持。一个方法是建立一个LLM链，执行以下任务：首先将查询从原始语言翻译成法语，然后像往常一样生成响应，最后将响应翻译回原始语言。

总结来说，考虑集成类似RAG系统来处理法律查询的读者应该谨慎行事，因为法律建议的复杂性和潜在影响。此类系统生成**虚假信息**的风险是一个需要关注的问题。尽管我们的评估在忠实度方面得分较高，但由于问题数量较少，且问题范围有限，加上评估模块本身的局限性，评估结果仍然存在不足。

为了应对这一问题，我们建议增加额外的评估层次和安全协议。此外，尽管该项目展示了高级RAG设置在生产中的能力，但它还不是一个最终定型的系统。这凸显了在全面部署之前进行持续完善和彻底测试的重要性。最后，用户还应当被提醒，系统的建议并不能替代专业的法律咨询。

# 参考文献

+   [llama-index高级概念](https://docs.llamaindex.ai/en/stable/getting_started/concepts/)

+   [llama-index评估](https://docs.llamaindex.ai/en/stable/optimizing/evaluation/evaluation/)

+   [Qdrant混合搜索](https://docs.llamaindex.ai/en/v0.10.20/examples/vector_stores/qdrant_hybrid.html)

+   [Qdrant快速嵌入](https://qdrant.tech/articles/fastembed/)

+   [llama-index路由器](https://docs.llamaindex.ai/en/stable/module_guides/querying/router/)

+   [互惠排名融合方法优于康多塞方法和单独的排名学习方法](https://plg.uwaterloo.ca/~gvcormac/cormacksigir09-rrf.pdf) 论文

# 联系方式

+   LinkedIn : [https://www.linkedin.com/in/hamza-gharbi-043045151/](https://www.linkedin.com/in/hamza-gharbi-043045151/)

+   Twitter : [https://twitter.com/HamzaGh25079790](https://twitter.com/HamzaGh25079790)
