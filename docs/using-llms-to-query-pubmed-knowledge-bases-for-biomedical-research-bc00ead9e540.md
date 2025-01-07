# 使用 LLMs 查询 PubMed 知识库进行生物医学研究

> 原文：[`towardsdatascience.com/using-llms-to-query-pubmed-knowledge-bases-for-biomedical-research-bc00ead9e540?source=collection_archive---------6-----------------------#2024-07-24`](https://towardsdatascience.com/using-llms-to-query-pubmed-knowledge-bases-for-biomedical-research-bc00ead9e540?source=collection_archive---------6-----------------------#2024-07-24)

## 为了乐趣和盈利使用 AI！

[](https://medium.com/@jilliandrowe?source=post_page---byline--bc00ead9e540--------------------------------)![Jillian Rowe](https://medium.com/@jilliandrowe?source=post_page---byline--bc00ead9e540--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bc00ead9e540--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bc00ead9e540--------------------------------) [Jillian Rowe](https://medium.com/@jilliandrowe?source=post_page---byline--bc00ead9e540--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bc00ead9e540--------------------------------) ·阅读时间：11 分钟·2024 年 7 月 24 日

--

![](img/162ee2641c1ac9a9bde0687955abfffe.png)

图片由 [🇸🇮 Janko Ferlič](https://unsplash.com/@itfeelslikefilm?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 第一部分：创建知识库

在本文中，我们将探讨如何利用大语言模型（LLMs）来搜索和科学论文，来自 [PubMed Open Access](https://www.ncbi.nlm.nih.gov/pmc/tools/openftlist/) 子集，这是一个用于访问生物医学和生命科学文献的免费资源。我们将使用检索增强生成（RAG）技术来搜索我们的数字图书馆。

AWS Bedrock 将作为我们的 AI 后端，PostgreSQL 作为存储嵌入的向量数据库，而 Python 中的 LangChain 库将用于导入论文并查询知识库。

如果你只关心通过查询知识库生成的结果，可以跳到文章末尾。

我们将重点关注的具体用例是查询与类风湿性关节炎相关的论文，这是一种影响关节的慢性炎症性疾病。我们将使用查询 `((rheumatoid arthritis) AND gene) AND cell` 从 PubMed 检索大约 10,000 篇相关论文，然后将其缩减到大约 5,000 篇作为我们的知识库。

# 免责声明

不是所有的研究文章或来源都有允许与 AI 一起使用的许可！

我没有包含所有源代码，因为 AI 库更新非常频繁，而且有许多方式可以配置知识库后端，但我提供了一些辅助函数，你可以跟着一起操作。

# PGVector：在向量数据库中存储嵌入

为了使 LLM 更容易处理和理解来自研究论文的文本数据，我们将把文本转换为数值嵌入，这些嵌入是文本的稠密向量表示。这些嵌入将使用 PGVector 库存储在 PostgreSQL 数据库中。这个步骤本质上将文本数据简化为一种 LLM 更容易处理的格式。

我正在运行一个本地的 PostgreSQL 数据库，对于我的数据集来说这样就足够了。托管 AWS Bedrock 知识库可能会很昂贵，而且我这个月并不打算让我的 AWS 账单飙升。现在是夏天，我得为孩子们的夏令营支付费用！

# AWS Bedrock：AI 后端

AWS Bedrock 是亚马逊云服务（AWS）提供的一项托管服务，允许你轻松部署和操作大型语言模型。在我们的设置中，Bedrock 将托管我们用于查询并从研究论文的知识库中检索相关信息的 LLM。

# LangChain：加载和查询知识库

LangChain 是一个 Python 库，简化了使用大型语言模型构建应用程序的过程。我们将使用 [LangChain](https://www.langchain.com/) 将我们的研究论文及其相关的嵌入载入知识库，并通过托管在 AWS Bedrock 上的大型语言模型（LLM）来查询这个知识库。

# 使用 PubGet 进行数据获取

尽管这个设置可以处理来自任何来源的研究论文，但我们使用 PubMed 是因为它是一个便捷的来源，可以根据特定的搜索查询获取大量论文。我们将使用 [PubGet](https://github.com/neuroquery/pubget) 工具来检索初始的 10,000 篇与类风湿关节炎、基因和细胞相关的论文。PubGet 背后会从 PubMed 的 FTP 服务获取文章。

```py
pubget run -q "((rheumatoid arthritis) AND gene) AND cell" \
 pubget_data
```

这将为我们获取 `xml` 格式的文章。

# 数据集的结构化与组织

除了技术方面，本文将重点讨论如何有效地构建和组织你的研究论文数据集。

1.  **数据集**：通过集合在全球范围内管理你的数据集。

1.  **元数据管理**：处理和整合与论文相关的元数据，例如作者信息、出版日期和关键词。

你需要提前考虑这一点。使用 LangChain 时，你根据集合查询数据集。每个集合都有一个名称和唯一标识符。

当你加载数据时，无论是 pdf 论文、xml 下载、markdown 文件、代码库、PowerPoint 幻灯片、文本文件等，你都可以附加额外的元数据。稍后你可以利用这些元数据来筛选结果。元数据是一个开放的字典，你可以添加标签、来源、表型或任何你认为可能相关的内容。

# 加载和查询知识库

文章还将涵盖将你的预处理和结构化数据集加载到知识库中的最佳实践，并提供如何有效查询知识库的示例，使用的 LLM 由 AWS Bedrock 托管。

到文章末尾时，你应该能充分理解如何利用 LLM 搜索并从大量研究论文中提取相关信息，同时掌握如何构建和组织数据集，以优化知识库的性能和准确性。

```py
import boto3
import pprint
import os
import boto3
import json
import hashlib
import funcy
import glob
from typing import Dict, Any, TypedDict, List
from langchain.llms.bedrock import Bedrock
from langchain.retrievers.bedrock import AmazonKnowledgeBasesRetriever
from langchain_core.documents import Document
from langchain_aws import ChatBedrock
from langchain_community.embeddings import BedrockEmbeddings # to create embeddings for the documents.
from langchain_experimental.text_splitter import SemanticChunker # to split documents into smaller chunks.
from langchain_text_splitters import CharacterTextSplitter
from langchain_postgres import PGVector
from pydantic import BaseModel, Field
from langchain_community.document_loaders import (
    WebBaseLoader,
    TextLoader,
    PyPDFLoader,
    CSVLoader,
    Docx2txtLoader,
    UnstructuredEPubLoader,
    UnstructuredMarkdownLoader,
    UnstructuredXMLLoader,
    UnstructuredRSTLoader,
    UnstructuredExcelLoader,
    DataFrameLoader,
)
import psycopg
import uuid
```

# 设置数据库连接

我正在本地运行一个[SupaBase](https://supabase.com/)的 PostgreSQL 数据库，通过他们的`docker-compose`设置运行。在生产环境中，我建议使用真正的数据库，比如 AWS AuroraDB，或者将 Supabase 运行在除你的笔记本电脑之外的地方。另外，记得把密码改成其他的，而不是“password”。

我没有注意到在较小的数据集上，AWS 托管的知识库和我的笔记本电脑之间有任何性能差异，但你的体验可能会不同。

```py
connection = f"postgresql+psycopg://{user}:{password}@{host}:{port}/{database}" 
# Establish the connection to the database
conn = psycopg.connect(
    conninfo = f"postgresql://{user}:{password}@{host}:{port}/{database}"
)
# Create a cursor to run queries
cur = conn.cursor()
```

# 使用 Langchain 将 AWS BedRock 嵌入插入表格

我们正在使用 AWS Bedrock 作为我们的 AI 知识库。我与大多数合作公司的工作中都有某种专有数据，而 Bedrock 保证你的数据将保持私密。你也可以使用任何其他 AI 后端。

```py
os.environ['AWS_DEFAULT_REGION'] = 'us-east-1'
bedrock_client              = boto3.client("bedrock-runtime") 
bedrock_embeddings          = BedrockEmbeddings(model_id="amazon.titan-embed-text-v1",client=bedrock_client)
bedrock_embeddings_image    = BedrockEmbeddings(model_id="amazon.titan-embed-image-v1",client=bedrock_client)
llm                         = ChatBedrock(model_id="anthropic.claude-3-sonnet-20240229-v1:0", client=bedrock_client)
```

```py
# function to create vector store
# make sure to update this if you change collections!
def create_vectorstore(embeddings,collection_name,conn):                       
    vectorstore = PGVector(
        embeddings=embeddings,
        collection_name=collection_name,
        connection=conn,
        use_jsonb=True,
    )
    return vectorstore
```

```py
def load_and_split_pdf_semantic(file_path, embeddings):
    loader = PyPDFLoader(file_path)
    pages = loader.load_and_split()
    return pages
```

```py
def load_xml(file_path, embeddings):
    loader = UnstructuredXMLLoader(
        file_path,
    )
    docs = loader.load_and_split()
    return docs
```

```py
def insert_embeddings(files, bedrock_embeddings, vectorstore):
    logging.info(f"Inserting {len(files)}")
    x = 1
    y = len(files)
    for file_path in files:
        logging.info(f"Splitting {file_path} {x}/{y}")
        docs = []
        if '.pdf' in file_path:
            try:
                with funcy.print_durations('process pdf'):                
                    docs = load_and_split_pdf_semantic(file_path, bedrock_embeddings)
            except Exception as e:
                logging.warning(f"Error loading docs")
        if '.xml' in file_path:
            try:
                with funcy.print_durations('process xml'):                
                    docs = load_xml(file_path, bedrock_embeddings)
            except Exception as e:
                logging.warning(e)
                logging.warning(f"Error loading docs")
        filtered_docs = []
        for d in docs:
            if len(d.page_content):
                filtered_docs.append(d)
        # Add documents to the vectorstore
        ids = []
        for d in filtered_docs:
            ids.append(
                hashlib.sha256(d.page_content.encode()).hexdigest()
            )

        if len(filtered_docs):
            texts = [ i.page_content for i in filtered_docs] 
            # metadata is a dictionary. You can add to it!
            metadatas = [ i.metadata for i in filtered_docs]
            #logging.info(f"Adding N: {len(filtered_docs)}")
            try:
                with funcy.print_durations('load psql'):
                    vectorstore.add_texts(texts=texts, metadatas = metadatas, ids=ids)
            except Exception as e:
                logging.warning(e)
                logging.warning(f"Error {x - 1}/{y}")
            #logging.info(f"Complete {x}/{y}")
        x = x + 1
```

```py
collection_name_text = "MY_COLLECTION" #pubmed, smiles, etc
vectorstore = create_vectorstore(bedrock_embeddings,collection_name_text,connection)
```

# 加载和处理 Pubmed XML 论文

我们的大部分数据是通过`pubget`工具获取的，文章格式为 XML。我们将使用 LangChain XML 加载器来处理、拆分和加载嵌入。

```py
files = glob.glob("/home/jovyan/data/pubget_ra/pubget_data/*/articles/*/*/article.xml")
```

```py
#I ran this previously
insert_embeddings(files[0:2], bedrock_embeddings, vectorstore)
```

# 加载和处理 Pubmed PDF 论文

PDF 格式更易于阅读，我抓取了一些用于与知识库进行问答测试。

```py
files = glob.glob("/home/jovyan/data/pubget_ra/papers/*pdf")
insert_embeddings(files[0:2], bedrock_embeddings, vectorstor
```

# 第二部分 — 查询知识库

现在我们已经设置好了知识库，可以使用增强型生成检索（RAG）方法，利用 LLM 运行查询。

我们的查询内容是：

+   告诉我关于类风湿性关节炎中的 T 细胞来源细胞因子的相关信息，并提供引用和文章标题。

+   告诉我关于类风湿性关节炎的单细胞研究。

+   告诉我关于类风湿性关节炎中的蛋白质-蛋白质关联。

+   告诉我关于类风湿性关节炎的 GWAS 研究发现。

```py
import hashlib
import logging
import os
from typing import Optional, List, Dict, Any
import glob
import boto3
from toolz.itertoolz import partition_all
import json
import funcy
import psycopg
from IPython.display import Markdown, display
from langchain.chains import create_retrieval_chain
from langchain.chains.combine_documents import create_stuff_documents_chain
from langchain.prompts import PromptTemplate
from langchain.retrievers.bedrock import (
    AmazonKnowledgeBasesRetriever,
    RetrievalConfig,
    VectorSearchConfig,
)
from aws_bedrock_utilities.models.base import BedrockBase, RAGResults
from aws_bedrock_utilities.models.pgvector_knowledgebase import BedrockPGWrapper
from sklearn.model_selection import train_test_split
import numpy as np
import pandas as pd
from pprint import pprint
import time
import logging
from rich.logging import RichHandler
```

我没有在这里列出它，但我总是会对我的知识库进行一些问答测试。选择一篇文章，解析出摘要或结论，然后向大型语言模型（LLM）提问。你应该能得到这篇文章的回复。

# 结构化你的查询

你首先需要知道你要查询的集合名称和查询内容。

我总是推荐先运行几个问答查询。以不同方式提问同样的显而易见的问题。

你还需要根据你的时间限制和知识库中的文章数量调整`MAX_DOCS_RETURNED`。LLM 会一直搜索，直到达到最大值，然后停止。如果需要全面搜索，你需要增加这个数值。

```py
# Make sure to keep the collection name consistent!
COLLECTION_NAME   = "MY_COLLECTION"
MAX_DOCS_RETURNED = 50
```

```py
p = BedrockPGWrapper(collection_name=COLLECTION_NAME)                              credentials.py:1147
```

```py
#model = "anthropic.claude-3-sonnet-20240229-v1:0"
model = "anthropic.claude-3-haiku-20240307-v1:0"
```

```py
model = "anthropic.claude-3-haiku-20240307-v1:0"
queries = [
    "Tell me about T cell–derived cytokines in relation to rheumatoid arthritis and provide citations and article titles",
    "Tell me about single-cell research in rheumatoid arthritis.",
    "Tell me about protein-protein associations in rheumatoid arthritis.",
    "Tell me about the findings of GWAS studies in rheumatoid arthritis.",

]
ai_responses = []
for query in queries:
    answer = p.run_kb_chat(query=query, collection_name= COLLECTION_NAME, model_id=model, search_kwargs={'k': MAX_DOCS_RETURNED, 'fetch_k': 50000 })
    ai_responses.append(answer)
    time.sleep(1)
```

```py
for answer in ai_responses:
    t = Markdown(f"""
### Query 
{answer['query']}### Response
{answer['result']}

    """)
    display(t)
```

# 第三部分：结果！

我们已经建立了知识库，运行了一些查询，现在我们准备查看 LLM 为我们生成的结果。

每个结果都是一个字典，包含原始查询、回应以及相关的源文档片段。

> 告诉我关于类风湿性关节炎中的 T 细胞来源细胞因子的相关信息，并提供引用和文章标题。
> 
> T 细胞衍生的细胞因子在类风湿关节炎（RA）的发病机制中起着关键作用。一些关键发现包括：
> 
> 已发现 RA 患者滑液中 IL-17 水平升高，IL-17 是由 Th17 细胞产生的细胞因子。IL-17 可以刺激成纤维样滑膜细胞（FLS）和巨噬细胞产生炎症介质，如 VEGF、IL-1、IL-6、TNF-α和前列腺素 E2，并促进破骨细胞形成，从而促进关节炎症和破坏（Honorati 等，2006 年，Schurgers 等，2011 年）。
> 
> 产生 IFN-γ的 Th1 细胞也与 RA 的发病机制相关。IFN-γ可以诱导巨噬细胞极化为促炎的 M1 表型（Schurgers 等，2011 年，Kebir 等，2009 年，Boniface 等，2010 年）。
> 
> CD161+ Th17 细胞能够同时产生 IL-17 和 IFN-γ，它们在 RA 患者的滑膜中富集，并可能促进炎症环境的形成（Afzali 等，2013 年，Bovenschen 等，2011 年，Koenen 等，2008 年，Pesenacker 等，2013 年）。
> 
> 通常抑制炎症的调节性 T 细胞（Tregs）在 RA 中功能受损，可能导致免疫反应失调（Moradi 等，2014 年，Samson 等，2012 年，Zhang 等，2018 年，Walter 等，2013 年，Wang 等，2018 年，Morita 等，2016 年）。
> 
> 总结来说，促炎 T 细胞亚群（Th1，Th17）与抗炎 Tregs 之间的不平衡是 RA 发病机制的标志，细胞因子如 IL-17 和 IFN-γ在推动关节炎症和破坏中起着核心作用。
> 
> 告诉我关于类风湿关节炎中的单细胞研究。
> 
> 单细胞研究为类风湿关节炎（RA）的发病机制提供了重要的见解：
> 
> 单细胞 RNA 测序（scRNA-seq）研究已经在 RA 滑膜中识别出不同的细胞状态和亚群，包括病理性 T 细胞亚群，如 T 外周辅助（Tph）细胞和细胞毒性 CD8+ T 细胞。
> 
> 对 RA 滑膜中 T 细胞受体（TCR）库的分析揭示了 CD4+和 CD8+ T 细胞群体的克隆扩增，表明存在抗原驱动的反应。
> 
> scRNA-seq 还表征了 RA 滑膜中扩展的活化 B 细胞、原始浆细胞和浆细胞群体，这些细胞群体展示了显著的克隆关系。
> 
> 基于 scRNA-seq 数据的受体-配体分析预测了关键的细胞间相互作用，例如 Tph 细胞与 B 细胞之间的相互作用，这可能推动滑膜炎症的发生。
> 
> 总体来说，单细胞研究揭示了 RA 滑膜中的细胞和分子异质性，识别了特定的免疫细胞亚群和可能作为个性化治疗靶点的途径。
> 
> 告诉我关于类风湿关节炎中的蛋白质-蛋白质相互作用。
> 
> 根据提供的背景信息，类风湿关节炎（RA）中一些关键的蛋白质-蛋白质相互作用包括：
> 
> 类风湿因子（RF）和抗环瓜氨酸蛋白抗体（ACPAs）：
> 
> 类风湿关节炎的前关节阶段约 80%的患者中发现类风湿因子（RF）。
> 
> ACPA 对 RA 具有高度特异性，并且可以在临床症状发作前数年检测到。
> 
> 蛋白精氨酸脱亚氨基酶（PAD）酶和抗 PAD 抗体：
> 
> 抗 PAD2 抗体与中等病程相关，而抗 PAD4 抗体与更严重和快速进展的 RA 相关。
> 
> 抗 PAD3/4 抗体可能预示着 RA 相关的间质性肺病的发展。
> 
> 抗碳酰化蛋白（anti-CarP）抗体：
> 
> 抗 CarP 抗体存在于 25-50%的 RA 患者中，与 RF 或 ACPA 阳性无关。
> 
> 抗 CarP 抗体与不良预后和增加的发病率相关，包括 RA 相关的间质性肺病。
> 
> 丙二醛-乙醛（MAA）加合物和抗 MAA 抗体：
> 
> 抗 MAA 抗体与无血清 RA 的放射学进展相关。
> 
> 信号通路中的蛋白质相互作用：
> 
> JAK-STAT、MAPK、PI3K-AKT 和 SYK 信号通路都涉及到 RA 的发病机制，并且是治疗干预的潜在靶点。
> 
> 总的来说，上下文突出了 RA 中几个关键的蛋白质相互作用，包括自身抗体（RF、ACPA、抗 PAD、抗 CarP、抗 MAA）和信号通路组分（JAK、STAT、MAPK、PI3K、SYK），这些在疾病的发病和进展中起着重要作用。
> 
> 告诉我关于类风湿性关节炎 GWAS 研究结果的情况。
> 
> 以下是类风湿性关节炎 GWAS 研究的一些关键发现：
> 
> 全基因组关联研究（GWAS）已经确定了 100 多个与类风湿性关节炎易感性相关的遗传风险位点。
> 
> HLA-DRB1 基因是最强的遗传风险因素，约占类风湿性关节炎遗传成分的 50%。含有“共享表位”序列的特定 HLA-DRB1 等位基因与增加的 RA 风险密切相关。
> 
> 通过 GWAS 识别的其他显著遗传风险因素包括 PTPN22、STAT4、CCR6、PADI4、CTLA4 和 CD40。这些基因参与免疫调节和炎症途径。
> 
> 遗传风险因素可能在血清阳性（ACPA 阳性）和血清阴性类风湿性关节炎之间有所不同。例如，HLA-DRB1 等位基因与血清阳性 RA 有更强的关联。
> 
> GWAS 还确定了与类风湿性关节炎疾病严重程度和治疗反应相关的遗传变异。例如，FCGR3A 和 PTPRC 基因的变异已与抗 TNF 疗法的反应相关联。
> 
> 总体而言，GWAS 为类风湿性关节炎的遗传结构和发病机制提供了重要见解，这对于开发靶向治疗和个性化治疗方法具有重要意义。

# 调查源文件

查询知识库将返回与源文件相关的片段。有时，langchain 返回的格式可能有些问题，但您可以随时返回源文件。

```py
x = 0
y = 10
for answer in ai_responses:
    for s in answer['source_documents']:
        if x <= y:
            print(s.metadata)
        else:
            break
        x = x + 1
```

```py
{'page': 6, 'source': '/home/jovyan/data/pubget_ra/papers/fimmu-12-790122.pdf'}
{'source': '/home/jovyan/data/pubget_ra/pubget_data/query_55c6003c0195b20fd4bdc411f67a8dcf/articles/d52/pmcid_11167034/article.xml'}
{'page': 7, 'source': '/home/jovyan/data/pubget_ra/papers/fimmu-12-790122.pdf'}
{'page': 17, 'source': '/home/jovyan/data/pubget_ra/papers/fimmu-12-790122.pdf'}
{'source': '/home/jovyan/data/pubget_ra/pubget_data/query_55c6003c0195b20fd4bdc411f67a8dcf/articles/657/pmcid_11151399/article.xml'}
{'page': 3, 'source': '/home/jovyan/data/pubget_ra/papers/41392_2023_Article_1331.pdf'}
{'page': 4, 'source': '/home/jovyan/data/pubget_ra/papers/41392_2023_Article_1331.pdf'}
{'page': 5, 'source': '/home/jovyan/data/pubget_ra/papers/fimmu-12-790122.pdf'}
{'source': '/home/jovyan/data/pubget_ra/pubget_data/query_55c6003c0195b20fd4bdc411f67a8dcf/articles/e02/pmcid_11219584/article.xml'}
{'page': 8, 'source': '/home/jovyan/data/pubget_ra/papers/fimmu-12-790122.pdf'}
{'source': '/home/jovyan/data/pubget_ra/pubget_data/query_55c6003c0195b20fd4bdc411f67a8dcf/articles/6fb/pmcid_11203675/article.xml'}
```

# 总结

就是这样！我们以低成本创建了一个知识库，使用了 AWS Bedrock 来加载嵌入（embeddings），然后用 Claude LLM 来执行查询。在这里我们使用了 PubMed 论文，但我们也可以使用会议记录、PowerPoint 幻灯片、爬取的网站，或是内部数据库。

如果你有任何问题、评论或教程请求，请随时通过电子邮件联系我，邮箱地址是 jillian@dabbleofdevops.com。
