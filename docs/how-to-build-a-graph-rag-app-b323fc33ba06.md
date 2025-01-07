# 如何构建一个Graph RAG应用

> 原文：[https://towardsdatascience.com/how-to-build-a-graph-rag-app-b323fc33ba06?source=collection_archive---------0-----------------------#2024-12-30](https://towardsdatascience.com/how-to-build-a-graph-rag-app-b323fc33ba06?source=collection_archive---------0-----------------------#2024-12-30)

![](../Images/9b638603daca1683e032a6df6bef86ee.png)

作者提供的图片

## 使用知识图谱和人工智能检索、筛选和总结医学期刊文章

[](https://stevehedden.medium.com/?source=post_page---byline--b323fc33ba06--------------------------------)[![Steve Hedden](../Images/af7bec4a191ab857eccd885dd89e88b4.png)](https://stevehedden.medium.com/?source=post_page---byline--b323fc33ba06--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b323fc33ba06--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b323fc33ba06--------------------------------) [Steve Hedden](https://stevehedden.medium.com/?source=post_page---byline--b323fc33ba06--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b323fc33ba06--------------------------------) ·阅读时间：25分钟·6天前

--

*该应用和笔记本的附带代码* [*在这里。*](https://github.com/SteveHedden/kg_llm/tree/main/graphRAGapp)

知识图谱（KGs）和大型语言模型（LLMs）是天作之合。我在[之前的文章](https://medium.com/towards-data-science/how-to-implement-knowledge-graphs-and-large-language-models-llms-together-at-the-enterprise-level-cf2835475c47)和[另一篇文章](https://medium.com/towards-data-science/how-to-implement-graph-rag-using-knowledge-graphs-and-vector-databases-60bb69a22759)中更详细地讨论了这两种技术的互补性，简而言之就是，“LLMs的主要弱点之一是它们是黑箱模型，且在事实知识方面存在困难，而这正是知识图谱的优势。知识图谱本质上是事实的集合，并且它们是完全可解释的。”

本文全部内容是关于构建一个简单的Graph RAG应用。那么，什么是RAG呢？RAG，或者说检索增强生成（Retrieval-Augmented Generation），是通过**检索**相关信息来**增强**发送给LLM的提示，从而**生成**响应。Graph RAG是将知识图谱作为检索部分的一部分的RAG。如果你从未听说过Graph RAG，或者想复习一下，我建议观看[这个视频](https://www.youtube.com/watch?v=knDDGYHnnSI)。

基本的想法是，与其将提示直接发送给没有经过您数据训练的LLM，不如用相关信息补充您的提示，以便LLM能够准确回答您的问题。我常用的一个例子是将职位描述和我的简历复制到ChatGPT中，以撰写求职信。如果我给它我的简历和我申请的职位描述，那么LLM能够为我的提示“写一封求职信”提供一个更加相关的回应。由于知识图谱是用来存储知识的，它们是存储内部数据并通过额外的上下文补充LLM提示的完美方式，从而提高响应的准确性和上下文理解能力。

这项技术有许多应用，如[客户服务机器人](https://arxiv.org/pdf/2404.17723)、[药物](https://academic.oup.com/bioinformatics/article/40/6/btae353/7687047) [发现](https://blog.biostrand.ai/integrating-knowledge-graphs-and-large-language-models-for-next-generation-drug-discovery)、[生命科学中的自动化监管报告生成](https://www.weave.bio/)、[HR的人才招聘和管理](https://beamery.com/resources/news/beamery-announces-talentgpt-the-world-s-first-generative-ai-for-hr)、[法律研究与写作](https://legal.thomsonreuters.com/blog/retrieval-augmented-generation-in-legal-tech/)以及[财富顾问助手](https://www.cnbc.com/amp/2023/03/14/morgan-stanley-testing-openai-powered-chatbot-for-its-financial-advisors.html)。由于其广泛的适用性以及提升LLM工具性能的潜力，Graph RAG（这是我在这里使用的术语）在受欢迎度上呈现爆炸式增长。以下是一个基于Google搜索的图表，展示了这一趋势。

![](../Images/ad71566165f3051525068ae4b36fe3b9.png)

来源：[https://trends.google.com/](https://trends.google.com/)

Graph RAG的搜索兴趣激增，甚至超越了“知识图谱”和“检索增强生成”这样的术语。请注意，Google Trends衡量的是*相对*的搜索兴趣，而不是搜索的绝对数量。2024年7月Graph RAG搜索量的激增与微软[宣布](https://www.microsoft.com/en-us/research/blog/graphrag-new-tool-for-complex-data-discovery-now-on-github/)其GraphRAG应用将在[GitHub](https://github.com/microsoft/graphrag)上线的那一周恰好重合。

然而，围绕Graph RAG的兴奋并不仅仅限于微软。三星在2024年7月收购了知识图谱公司RDFox。该[收购公告文章](https://news.samsung.com/global/samsung-electronics-announces-acquisition-of-oxford-semantic-technologies-uk-based-knowledge-graph-startup)并未明确提到Graph RAG，但在2024年11月发表的[《福布斯》文章](https://www.forbes.com/sites/zakdoffman/2024/11/09/samsung-confirms-new-upgrade-choice-millions-of-galaxy-owners-must-now-decide/)中，一位三星发言人表示：“我们计划开发知识图谱技术，作为个性化AI的核心技术之一，并与生成式AI有机结合，以支持面向用户的服务。”

2024年10月，领先的图数据库公司Ontotext与语义网公司Semantic Web公司合并，成立了[Graphwise](https://graphwise.ai/)。根据[新闻稿](https://www.prnewswire.com/news-releases/semantic-web-company-and-ontotext-merge-to-create-knowledge-graph-and-ai-powerhouse-graphwise-302283427.html?utm_source=chatgpt.com)，此次合并的目标是“使图谱RAG作为一个类别的演进更加普及”。

尽管围绕Graph RAG的一些热议可能源于人们对聊天机器人和生成式AI的广泛兴奋，但它反映了知识图谱在解决复杂的现实问题方面的真正演进。一个例子是LinkedIn [应用了Graph RAG](https://arxiv.org/pdf/2404.17723)来改善其客户服务技术支持。由于该工具能够检索相关数据（如先前解决的类似工单或问题）供LLM使用，回答更加准确，平均解决时间从40小时减少到了15小时。

本文将通过构建一个相当简单，但我认为具有代表性的例子，展示Graph RAG如何在实践中工作。最终结果是一个非技术用户可以交互的应用程序。像我上一篇文章一样，我将使用由PubMed的医学期刊文章组成的数据集。这个应用程序的目的是让医学领域的人可以用来进行文献综述。然而，同样的原则也可以应用于许多其他用例，这就是Graph RAG如此令人兴奋的原因。

该应用程序的结构，以及本文的内容如下：

第零步是准备数据。下面我将详细解释，但总体目标是将原始数据向量化，并单独将其转化为RDF图谱。只要在向量化之前我们保持与文章的URI关联，我们就可以在文章图谱和文章向量空间之间进行导航。然后，我们可以：

1.  **搜索文章：**利用向量数据库的强大功能，根据搜索词初步搜索相关的文章。我将使用向量相似度来检索与搜索词最相似的文章。

1.  **细化术语：** 探索[医学主题词（MeSH）生物医学词汇表](https://id.nlm.nih.gov/mesh/)以选择用于过滤步骤1中文章的术语。这个控制词汇表包含医学术语、替代名称、更窄的概念以及许多其他属性和关系。

1.  **过滤与总结：** 使用MeSH术语来过滤文章，以避免“上下文污染”。然后将剩余的文章与附加提示一起发送给LLM，比如：“用要点总结。”

在我们开始之前，有一些关于此应用和教程的说明：

+   这个设置仅使用知识图谱来处理元数据。这之所以可行，是因为我数据集中的每篇文章已经被标注上了属于丰富控制词汇的术语。我使用图谱来处理结构和语义，使用向量数据库来进行基于相似度的检索，确保每项技术都用于它最擅长的领域。向量相似度可以告诉我们“食管癌”与“口腔癌”在语义上相似，但知识图谱可以告诉我们“食管癌”和“口腔癌”之间关系的详细信息。

+   我用于此应用的数据集来自PubMed的医学期刊文章（数据详情见下文）。我选择这个数据集是因为它是结构化的（表格形式），但也包含每篇文章的摘要文本，并且已经用与公认的控制词汇（MeSH）对齐的主题术语进行了标注。由于这些是医学文章，我将这个应用命名为“医学图谱RAG”。但这种结构可以应用于任何领域，并不限于医学领域。

+   我希望本教程和应用程序展示的是，通过在检索步骤中加入知识图谱，你可以提高RAG应用的准确性和可解释性。我将展示知识图谱如何通过两种方式提高RAG应用的准确性：一种是为用户提供过滤上下文的方式，确保LLM只接收到最相关的信息；另一种是通过使用由领域专家维护和策划的、具有密切关系的特定领域控制词汇来进行过滤。

+   本教程和应用程序没有直接展示的两个重要方面是知识图谱（KGs）如何增强RAG应用程序：治理、访问控制和合规性；以及效率和可扩展性。在治理方面，KGs不仅可以过滤相关内容以提高准确性——它们还可以执行数据治理政策。例如，如果用户没有权限访问某些内容，那么这些内容可以从他们的RAG流程中排除。在效率和可扩展性方面，KGs可以帮助确保RAG应用程序不会被“搁置”。虽然创建一个令人印象深刻的单一RAG应用程序很容易（这正是本教程的目的），但许多公司却在大量孤立的POC（概念验证）中挣扎，这些POC缺乏一个统一的框架、结构或平台。这意味着这些应用程序中的许多可能难以长期生存。由KGs驱动的元数据层可以打破数据孤岛，提供建立、扩展和有效维护RAG应用程序所需的基础。使用像MeSH这样丰富的受控词汇作为这些文章的元数据标签，是确保该图形RAG应用程序能够与其他系统集成并减少成为孤岛风险的方式。

# 步骤 0：准备数据

*准备数据的代码在* [*这个*](https://github.com/SteveHedden/kg_llm/blob/main/graphRAGapp/VectorVsKG_updated.ipynb) *笔记本中。*

如前所述，我再次决定使用[这个](https://www.kaggle.com/datasets/owaiskhan9654/pubmed-multilabel-text-classification)来自PubMed库的50,000篇研究文章数据集（许可证[CC0: 公共领域](https://creativecommons.org/publicdomain/zero/1.0/)）。该数据集包含文章的标题、摘要以及一个元数据标签字段。这些标签来自医学主题词表（MeSH）受控词汇库。PubMed文章实际上只是文章的元数据——每篇文章都有摘要，但我们没有完整的文本。数据已经是表格格式，并附有MeSH术语的标签。

我们可以直接对这个表格数据集进行向量化。我们本可以在向量化之前将其转换为图（RDF），但我在这个应用程序中没有这样做，而且我不确定对这种数据类型最终结果是否有帮助。向量化原始数据最重要的一点是，我们首先为每篇文章添加[统一资源标识符](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier)（URI）。URI是用于导航RDF数据的唯一标识符，它对于在向量和图中的实体之间来回切换是必需的。此外，我们将为MeSH术语在向量数据库中创建一个单独的集合。这将使用户能够在不事先了解受控词汇的情况下搜索相关术语。下面是我们为准备数据所做工作的示意图。

![](../Images/13719b544747a36938663e2a3e3239fa.png)

图片由作者提供

我们在向量数据库中有两个可以查询的集合：文章和术语。我们还有以 RDF 格式表示的数据作为图。由于 MeSH 有一个 API，我将直接查询该 API 以获取术语的替代名称和更窄的概念。

## 在 Weaviate 中向量化数据

首先导入所需的包并设置 Weaviate 客户端：

```py
import weaviate
from weaviate.util import generate_uuid5
from weaviate.classes.init import Auth
import os
import json
import pandas as pd

client = weaviate.connect_to_weaviate_cloud(
    cluster_url="XXX",  # Replace with your Weaviate Cloud URL
    auth_credentials=Auth.api_key("XXX"),  # Replace with your Weaviate Cloud key
    headers={'X-OpenAI-Api-key': "XXX"}  # Replace with your OpenAI API key
)
```

读取 PubMed 期刊文章。我使用的是[Databricks](https://www.databricks.com/)来运行这个笔记本，因此根据你运行的环境，可能需要进行更改。这里的目标只是将数据加载到 pandas DataFrame 中。

```py
df = spark.sql("SELECT * FROM workspace.default.pub_med_multi_label_text_classification_dataset_processed").toPandas()
```

如果你是在本地运行，只需执行以下操作：

```py
df = pd.read_csv("PubMed Multi Label Text Classification Dataset Processed.csv")
```

然后稍微清理一下数据：

```py
import numpy as np
# Replace infinity values with NaN and then fill NaN values
df.replace([np.inf, -np.inf], np.nan, inplace=True)
df.fillna('', inplace=True)

# Convert columns to string type
df['Title'] = df['Title'].astype(str)
df['abstractText'] = df['abstractText'].astype(str)
df['meshMajor'] = df['meshMajor'].astype(str)
```

现在，我们需要为每篇文章创建一个 URI，并将其作为新列添加。这非常重要，因为 URI 是将文章的向量表示与文章的知识图谱表示相连接的方式。

```py
import urllib.parse
from rdflib import Graph, RDF, RDFS, Namespace, URIRef, Literal

# Function to create a valid URI
def create_valid_uri(base_uri, text):
    if pd.isna(text):
        return None
    # Encode text to be used in URI
    sanitized_text = urllib.parse.quote(text.strip().replace(' ', '_').replace('"', '').replace('<', '').replace('>', '').replace("'", "_"))
    return URIRef(f"{base_uri}/{sanitized_text}")

# Function to create a valid URI for Articles
def create_article_uri(title, base_namespace="http://example.org/article/"):
    """
    Creates a URI for an article by replacing non-word characters with underscores and URL-encoding.

    Args:
        title (str): The title of the article.
        base_namespace (str): The base namespace for the article URI.

    Returns:
        URIRef: The formatted article URI.
    """
    if pd.isna(title):
        return None
    # Replace non-word characters with underscores
    sanitized_title = re.sub(r'\W+', '_', title.strip())
    # Condense multiple underscores into a single underscore
    sanitized_title = re.sub(r'_+', '_', sanitized_title)
    # URL-encode the term
    encoded_title = quote(sanitized_title)
    # Concatenate with base_namespace without adding underscores
    uri = f"{base_namespace}{encoded_title}"
    return URIRef(uri)

# Add a new column to the DataFrame for the article URIs
df['Article_URI'] = df['Title'].apply(lambda title: create_valid_uri("http://example.org/article", title))
```

我们还需要创建一个包含所有用于标记文章的 MeSH 术语的 DataFrame。稍后在搜索相似的 MeSH 术语时，这将非常有用。

```py
# Function to clean and parse MeSH terms
def parse_mesh_terms(mesh_list):
    if pd.isna(mesh_list):
        return []
    return [
        term.strip().replace(' ', '_')
        for term in mesh_list.strip("[]'").split(',')
    ]

# Function to create a valid URI for MeSH terms
def create_valid_uri(base_uri, text):
    if pd.isna(text):
        return None
    sanitized_text = urllib.parse.quote(
        text.strip()
        .replace(' ', '_')
        .replace('"', '')
        .replace('<', '')
        .replace('>', '')
        .replace("'", "_")
    )
    return f"{base_uri}/{sanitized_text}"

# Extract and process all MeSH terms
all_mesh_terms = []
for mesh_list in df["meshMajor"]:
    all_mesh_terms.extend(parse_mesh_terms(mesh_list))

# Deduplicate terms
unique_mesh_terms = list(set(all_mesh_terms))

# Create a DataFrame of MeSH terms and their URIs
mesh_df = pd.DataFrame({
    "meshTerm": unique_mesh_terms,
    "URI": [create_valid_uri("http://example.org/mesh", term) for term in unique_mesh_terms]
})

# Display the DataFrame
print(mesh_df)
```

对文章 DataFrame 进行向量化：

```py
from weaviate.classes.config import Configure

#define the collection
articles = client.collections.create(
    name = "Article",
    vectorizer_config=Configure.Vectorizer.text2vec_openai(),  # If set to "none" you must always provide vectors yourself. Could be any other "text2vec-*" also.
    generative_config=Configure.Generative.openai(),  # Ensure the `generative-openai` module is used for generative queries
)

#add ojects
articles = client.collections.get("Article")

with articles.batch.dynamic() as batch:
    for index, row in df.iterrows():
        batch.add_object({
            "title": row["Title"],
            "abstractText": row["abstractText"],
            "Article_URI": row["Article_URI"],
            "meshMajor": row["meshMajor"],
        })
```

现在对 MeSH 术语进行向量化：

```py
#define the collection
terms = client.collections.create(
    name = "term",
    vectorizer_config=Configure.Vectorizer.text2vec_openai(),  # If set to "none" you must always provide vectors yourself. Could be any other "text2vec-*" also.
    generative_config=Configure.Generative.openai(),  # Ensure the `generative-openai` module is used for generative queries
)

#add ojects
terms = client.collections.get("term")

with terms.batch.dynamic() as batch:
    for index, row in mesh_df.iterrows():
        batch.add_object({
            "meshTerm": row["meshTerm"],
            "URI": row["URI"],
        })
```

现在，你可以直接对向量化数据集运行语义搜索、相似性搜索和 RAG。我在这里不会详细讲解这些，但你可以查看我[随附的笔记本](https://github.com/SteveHedden/kg_llm/tree/main/graphRAGapp)中的代码来实现这些功能。

## 将数据转化为知识图谱

我只是使用了我们在[上一篇文章](https://medium.com/towards-data-science/how-to-implement-graph-rag-using-knowledge-graphs-and-vector-databases-60bb69a22759)中使用的相同代码来实现这一点。我们基本上是将数据中的每一行转化为我们知识图谱中的“文章”实体。然后，我们为这些文章提供标题、摘要和 MeSH 术语等属性。我们还将每个 MeSH 术语也转化为一个实体。此代码还为每篇文章添加了随机的发布日期（作为名为 date published 的属性）和介于 1 到 10 之间的随机数字（作为名为 access 的属性）。我们在这个演示中不会使用这些属性。下面是我们从数据中创建的图的可视化表示。

![](../Images/2390e2c00105e19f3f794a8c78acaf7a.png)

图片来源：作者

下面是如何遍历 DataFrame 并将其转化为 RDF 数据：

```py
from rdflib import Graph, RDF, RDFS, Namespace, URIRef, Literal
from rdflib.namespace import SKOS, XSD
import pandas as pd
import urllib.parse
import random
from datetime import datetime, timedelta
import re
from urllib.parse import quote

# --- Initialization ---
g = Graph()

# Define namespaces
schema = Namespace('http://schema.org/')
ex = Namespace('http://example.org/')
prefixes = {
    'schema': schema,
    'ex': ex,
    'skos': SKOS,
    'xsd': XSD
}
for p, ns in prefixes.items():
    g.bind(p, ns)

# Define classes and properties
Article = URIRef(ex.Article)
MeSHTerm = URIRef(ex.MeSHTerm)
g.add((Article, RDF.type, RDFS.Class))
g.add((MeSHTerm, RDF.type, RDFS.Class))

title = URIRef(schema.name)
abstract = URIRef(schema.description)
date_published = URIRef(schema.datePublished)
access = URIRef(ex.access)

g.add((title, RDF.type, RDF.Property))
g.add((abstract, RDF.type, RDF.Property))
g.add((date_published, RDF.type, RDF.Property))
g.add((access, RDF.type, RDF.Property))

# Function to clean and parse MeSH terms
def parse_mesh_terms(mesh_list):
    if pd.isna(mesh_list):
        return []
    return [term.strip() for term in mesh_list.strip("[]'").split(',')]

# Enhanced convert_to_uri function
def convert_to_uri(term, base_namespace="http://example.org/mesh/"):
    """
    Converts a MeSH term into a standardized URI by replacing spaces and special characters with underscores,
    ensuring it starts and ends with a single underscore, and URL-encoding the term.

    Args:
        term (str): The MeSH term to convert.
        base_namespace (str): The base namespace for the URI.

    Returns:
        URIRef: The formatted URI.
    """
    if pd.isna(term):
        return None  # Handle NaN or None terms gracefully

    # Step 1: Strip existing leading and trailing non-word characters (including underscores)
    stripped_term = re.sub(r'^\W+|\W+$', '', term)

    # Step 2: Replace non-word characters with underscores (one or more)
    formatted_term = re.sub(r'\W+', '_', stripped_term)

    # Step 3: Replace multiple consecutive underscores with a single underscore
    formatted_term = re.sub(r'_+', '_', formatted_term)

    # Step 4: URL-encode the term to handle any remaining special characters
    encoded_term = quote(formatted_term)

    # Step 5: Add single leading and trailing underscores
    term_with_underscores = f"_{encoded_term}_"

    # Step 6: Concatenate with base_namespace without adding an extra underscore
    uri = f"{base_namespace}{term_with_underscores}"

    return URIRef(uri)

# Function to generate a random date within the last 5 years
def generate_random_date():
    start_date = datetime.now() - timedelta(days=5*365)
    random_days = random.randint(0, 5*365)
    return start_date + timedelta(days=random_days)

# Function to generate a random access value between 1 and 10
def generate_random_access():
    return random.randint(1, 10)

# Function to create a valid URI for Articles
def create_article_uri(title, base_namespace="http://example.org/article"):
    """
    Creates a URI for an article by replacing non-word characters with underscores and URL-encoding.

    Args:
        title (str): The title of the article.
        base_namespace (str): The base namespace for the article URI.

    Returns:
        URIRef: The formatted article URI.
    """
    if pd.isna(title):
        return None
    # Encode text to be used in URI
    sanitized_text = urllib.parse.quote(title.strip().replace(' ', '_').replace('"', '').replace('<', '').replace('>', '').replace("'", "_"))
    return URIRef(f"{base_namespace}/{sanitized_text}")

# Loop through each row in the DataFrame and create RDF triples
for index, row in df.iterrows():
    article_uri = create_article_uri(row['Title'])
    if article_uri is None:
        continue

    # Add Article instance
    g.add((article_uri, RDF.type, Article))
    g.add((article_uri, title, Literal(row['Title'], datatype=XSD.string)))
    g.add((article_uri, abstract, Literal(row['abstractText'], datatype=XSD.string)))

    # Add random datePublished and access
    random_date = generate_random_date()
    random_access = generate_random_access()
    g.add((article_uri, date_published, Literal(random_date.date(), datatype=XSD.date)))
    g.add((article_uri, access, Literal(random_access, datatype=XSD.integer)))

    # Add MeSH Terms
    mesh_terms = parse_mesh_terms(row['meshMajor'])
    for term in mesh_terms:
        term_uri = convert_to_uri(term, base_namespace="http://example.org/mesh/")
        if term_uri is None:
            continue

        # Add MeSH Term instance
        g.add((term_uri, RDF.type, MeSHTerm))
        g.add((term_uri, RDFS.label, Literal(term.replace('_', ' '), datatype=XSD.string)))

        # Link Article to MeSH Term
        g.add((article_uri, schema.about, term_uri))

# Path to save the file
file_path = "/Workspace/PubMedGraph.ttl"

# Save the file
g.serialize(destination=file_path, format='turtle')

print(f"File saved at {file_path}")
```

好的，现在我们已经有了数据的向量化版本和图（RDF）版本。每个向量都有一个与之关联的 URI，URI 对应于知识图谱中的一个实体，因此我们可以在数据格式之间来回转换。

# 构建应用

我决定使用[Streamlit](https://streamlit.io/)来构建这个图 RAG 应用的界面。与上一篇博客文章类似，我保持了相同的用户流程。

1.  **搜索文章：** 首先，用户使用搜索词进行文章搜索。这完全依赖于向量数据库。用户的搜索词会被发送到向量数据库，返回与该词在向量空间中最接近的十篇文章。

1.  **优化术语：** 第二，用户决定使用哪些MeSH术语来过滤返回的结果。由于我们也对MeSH术语进行了向量化处理，因此用户可以输入自然语言提示，以获得最相关的MeSH术语。接着，我们允许用户展开这些术语，查看它们的替代名称和更窄的概念。用户可以选择任意数量的术语作为过滤条件。

1.  **过滤与总结：** 第三，用户将选择的术语应用为过滤条件，作用于原始的十篇期刊文章。由于PubMed文章已经标注了MeSH术语，我们可以实现这一功能。最后，我们允许用户输入额外的提示，并将其与过滤后的期刊文章一起发送给LLM。这是RAG应用中的生成步骤。

让我们逐步完成这些操作。你可以在我的GitHub上查看完整的应用和代码，但以下是其结构：

```py
-- app.py (a python file that drives the app and calls other functions as needed)
-- query_functions (a folder containing python files with queries)
  -- rdf_queries.py (python file with RDF queries)
  -- weaviate_queries.py (python file containing weaviate queries)
-- PubMedGraph.ttl (the pubmed data in RDF format, stored as a ttl file)
```

## 搜索文章

首先，我们要做的是实现Weaviate的[向量相似性搜索](https://weaviate.io/developers/weaviate/search/similarity)。由于我们的文章已被向量化，我们可以将搜索词发送到向量数据库，并返回相似的文章。

![](../Images/d4a996d6b04a9058f4313cf481470501.png)

作者插图

在app.py中，搜索相关期刊文章的主要函数如下所示：

```py
# --- TAB 1: Search Articles ---
with tab_search:
    st.header("Search Articles (Vector Query)")
    query_text = st.text_input("Enter your vector search term (e.g., Mouth Neoplasms):", key="vector_search")

    if st.button("Search Articles", key="search_articles_btn"):
        try:
            client = initialize_weaviate_client()
            article_results = query_weaviate_articles(client, query_text)

            # Extract URIs here
            article_uris = [
                result["properties"].get("article_URI")
                for result in article_results
                if result["properties"].get("article_URI")
            ]

            # Store article_uris in the session state
            st.session_state.article_uris = article_uris

            st.session_state.article_results = [
                {
                    "Title": result["properties"].get("title", "N/A"),
                    "Abstract": (result["properties"].get("abstractText", "N/A")[:100] + "..."),
                    "Distance": result["distance"],
                    "MeSH Terms": ", ".join(
                        ast.literal_eval(result["properties"].get("meshMajor", "[]"))
                        if result["properties"].get("meshMajor") else []
                    ),

                }
                for result in article_results
            ]
            client.close()
        except Exception as e:
            st.error(f"Error during article search: {e}")

    if st.session_state.article_results:
        st.write("**Search Results for Articles:**")
        st.table(st.session_state.article_results)
    else:
        st.write("No articles found yet.")
```

该功能使用存储在weaviate_queries中的查询来建立Weaviate客户端（initialize_weaviate_client），并搜索文章（query_weaviate_articles）。然后，我们将返回的文章以表格形式展示，包括它们的摘要、距离（与搜索词的接近程度）以及它们所标记的MeSH术语。

在weaviate_queries.py中查询Weaviate的函数如下所示：

```py
# Function to query Weaviate for Articles
def query_weaviate_articles(client, query_text, limit=10):
    # Perform vector search on Article collection
    response = client.collections.get("Article").query.near_text(
        query=query_text,
        limit=limit,
        return_metadata=MetadataQuery(distance=True)
    )

    # Parse response
    results = []
    for obj in response.objects:
        results.append({
            "uuid": obj.uuid,
            "properties": obj.properties,
            "distance": obj.metadata.distance,
        })
    return results
```

如你所见，我在这里将结果限制为十个，以简化操作，但你可以更改这个限制。这只是使用Weaviate中的向量相似性搜索来返回相关结果。

应用中的最终结果如下所示：

![](../Images/d18988b667f6edfe53393d752ef9b6c1.png)

作者插图

作为演示，我将搜索“口腔癌治疗方法”这一术语。如你所见，返回了10篇文章，且大多相关。这展示了基于向量的检索的优点与局限性。

优势在于，我们可以用最少的努力，在数据上构建语义搜索功能。如上所示，我们所做的只是设置客户端并将数据发送到向量数据库。一旦数据被向量化，我们就可以进行语义搜索、相似性搜索，甚至是RAG。我已经将部分内容放入了与本文配套的笔记本中，但在Weaviate的[官方文档](https://weaviate.io/developers/weaviate)中有更多详细信息。

如我之前所提到的，基于向量的检索的弱点是它们是黑盒模型，并且在处理事实性知识时存在困难。在我们的示例中，看起来大多数文章都是关于某种癌症治疗或疗法的。有些文章专门讨论口腔癌，有些则讨论口腔癌的亚型，如牙龈癌（牙龈癌）和腭癌（腭癌）。但也有关于鼻咽癌（上咽部癌症）、下颌癌（下颌癌）和食管癌（食管癌）的文章。所有这些（上咽部、下颌或食管癌）都不被认为是口腔癌。可以理解，关于针对鼻咽肿瘤的特定癌症放疗的文章可能会与“口腔癌治疗”这一提示相关，但如果你仅仅是想寻找口腔癌的治疗方法，它可能并不相关。如果我们将这十篇文章直接输入到LLM的提示中并要求它“总结不同的治疗选项”，我们将得到错误的信息。

RAG的目的是为大语言模型（LLM）提供一组非常具体的附加信息，以便更好地回答您的问题——如果这些信息不正确或不相关，可能会导致LLM给出误导性回答。这通常被称为“上下文污染”。上下文污染特别危险的一点是，回答不一定是事实不准确的（LLM可能准确地总结了我们提供的治疗选项），也不一定是基于不准确的数据（假设期刊文章本身是准确的），只是使用了错误的数据来回答您的问题。在这个例子中，用户可能正在阅读如何治疗错误类型癌症的文章，这看起来是非常糟糕的。

## 精炼术语

知识图谱（KGs）可以通过精炼来自向量数据库的结果，帮助提高回答的准确性，并减少上下文污染的可能性。下一步是选择我们希望使用的MeSH术语来筛选文章。首先，我们在术语集合中对向量数据库进行另一次向量相似度搜索。这样做是因为用户可能不熟悉MeSH控制词汇。在我们上面的示例中，我搜索了“口腔癌的治疗方法”，但是“口腔癌”不是MeSH中的术语——他们使用的是“口腔肿瘤”（Mouth Neoplasms）。我们希望用户能够在没有事先了解这些术语的情况下开始探索MeSH术语——无论使用什么元数据来标记内容，这都是一种良好的实践。

![](../Images/e18e44bd8931abf6ec9b70a67d17cdad.png)

图片来自作者

获取相关MeSH术语的功能与之前的Weaviate查询几乎相同。只需要将文章替换为术语：

```py
# Function to query Weaviate for MeSH Terms
def query_weaviate_terms(client, query_text, limit=10):
    # Perform vector search on MeshTerm collection
    response = client.collections.get("term").query.near_text(
        query=query_text,
        limit=limit,
        return_metadata=MetadataQuery(distance=True)
    )

    # Parse response
    results = []
    for obj in response.objects:
        results.append({
            "uuid": obj.uuid,
            "properties": obj.properties,
            "distance": obj.metadata.distance,
        })
    return results
```

这是在应用程序中的样子：

![](../Images/b518eb952de2b3ad25305e1d144d2006.png)

图片来自作者

如你所见，我搜索了“口腔癌”（mouth cancer），返回了最相似的术语。口腔癌没有被返回，因为这不是MeSH中的术语，但口腔肿瘤（Mouth Neoplasms）在列表中。

下一步是允许用户展开返回的术语，以查看替代名称和更具体的概念。这需要查询[MeSH API](https://id.nlm.nih.gov/mesh/)。这是这个应用中最棘手的部分，原因有很多。最大的问题是Streamlit要求每个项目都有唯一的ID，而MeSH术语可能会重复——如果返回的某个概念是另一个概念的子概念，那么当你展开父概念时，会出现重复的子概念。我认为我已经解决了大部分问题，应用应该能正常运行，但这个阶段可能还会有bug。

我们依赖的函数可以在rdf_queries.py中找到。我们需要一个来获取术语的替代名称：

```py
# Fetch alternative names and triples for a MeSH term
def get_concept_triples_for_term(term):
    term = sanitize_term(term)  # Sanitize input term
    sparql = SPARQLWrapper("https://id.nlm.nih.gov/mesh/sparql")
    query = f"""
    PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
    PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
    PREFIX meshv: <http://id.nlm.nih.gov/mesh/vocab#>
    PREFIX mesh: <http://id.nlm.nih.gov/mesh/>

    SELECT ?subject ?p ?pLabel ?o ?oLabel
    FROM <http://id.nlm.nih.gov/mesh>
    WHERE {{
        ?subject rdfs:label "{term}"@en .
        ?subject ?p ?o .
        FILTER(CONTAINS(STR(?p), "concept"))
        OPTIONAL {{ ?p rdfs:label ?pLabel . }}
        OPTIONAL {{ ?o rdfs:label ?oLabel . }}
    }}
    """
    try:
        sparql.setQuery(query)
        sparql.setReturnFormat(JSON)
        results = sparql.query().convert()

        triples = set()
        for result in results["results"]["bindings"]:
            obj_label = result.get("oLabel", {}).get("value", "No label")
            triples.add(sanitize_term(obj_label))  # Sanitize term before adding

        # Add the sanitized term itself to ensure it's included
        triples.add(sanitize_term(term))
        return list(triples)

    except Exception as e:
        print(f"Error fetching concept triples for term '{term}': {e}")
        return []
```

我们还需要一些功能来获取给定术语的更具体（子概念）概念。我有两个函数实现了这一点——一个获取术语的直接子概念，另一个递归函数返回给定深度的所有子概念。

```py
# Fetch narrower concepts for a MeSH term
def get_narrower_concepts_for_term(term):
    term = sanitize_term(term)  # Sanitize input term
    sparql = SPARQLWrapper("https://id.nlm.nih.gov/mesh/sparql")
    query = f"""
    PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
    PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
    PREFIX meshv: <http://id.nlm.nih.gov/mesh/vocab#>
    PREFIX mesh: <http://id.nlm.nih.gov/mesh/>

    SELECT ?narrowerConcept ?narrowerConceptLabel
    WHERE {{
        ?broaderConcept rdfs:label "{term}"@en .
        ?narrowerConcept meshv:broaderDescriptor ?broaderConcept .
        ?narrowerConcept rdfs:label ?narrowerConceptLabel .
    }}
    """
    try:
        sparql.setQuery(query)
        sparql.setReturnFormat(JSON)
        results = sparql.query().convert()

        concepts = set()
        for result in results["results"]["bindings"]:
            subject_label = result.get("narrowerConceptLabel", {}).get("value", "No label")
            concepts.add(sanitize_term(subject_label))  # Sanitize term before adding

        return list(concepts)

    except Exception as e:
        print(f"Error fetching narrower concepts for term '{term}': {e}")
        return []

# Recursive function to fetch narrower concepts to a given depth
def get_all_narrower_concepts(term, depth=2, current_depth=1):
    term = sanitize_term(term)  # Sanitize input term
    all_concepts = {}
    try:
        narrower_concepts = get_narrower_concepts_for_term(term)
        all_concepts[sanitize_term(term)] = narrower_concepts

        if current_depth < depth:
            for concept in narrower_concepts:
                child_concepts = get_all_narrower_concepts(concept, depth, current_depth + 1)
                all_concepts.update(child_concepts)

    except Exception as e:
        print(f"Error fetching all narrower concepts for term '{term}': {e}")

    return all_concepts
```

第2步的另一个重要部分是允许用户选择术语并将其添加到“已选择术语”列表中。这些术语将出现在屏幕左侧的侧边栏中。有很多方法可以改进这一步，例如：

+   没有办法清除所有内容，但你可以清除缓存或刷新浏览器（如果需要的话）。

+   目前没有办法“选择所有更具体的概念”，这将会很有帮助。

+   目前没有添加过滤规则的选项。现在我们只是简单地假设文章必须包含术语A或术语B或术语C等。最后的排名是基于文章被标记的术语数量。

这是在应用中看到的样子：

![](../Images/92e6f9744b8b568b723112f82f41339b.png)

图片来自作者

我可以展开口腔肿瘤（Mouth Neoplasms）来查看替代名称，在这种情况下是“口腔癌”，以及所有更具体的概念。如你所见，大多数更具体的概念都有自己的子概念，你也可以展开这些子概念。为了这个演示的目的，我将选择口腔肿瘤的所有子概念。

![](../Images/587f3974e8d7406677192e774d96824b.png)

图片来自作者

这一步很重要，不仅因为它允许用户过滤搜索结果，还因为它为用户提供了探索MeSH图谱并从中学习的方式。例如，在这里用户可以学到鼻咽肿瘤并不是口腔肿瘤的子集。

## 过滤和总结

现在你已经有了文章和过滤条件，可以应用过滤器并总结结果。在这一步中，我们将第一步返回的原始10篇文章与精炼后的MeSH术语列表结合在一起。我们允许用户在将提示发送给LLM之前添加额外的上下文。

![](../Images/755f10265aed07b0b49c8a86f6fe1d67.png)

图片来自作者

我们进行过滤的方法是，我们需要从原始搜索中获取这10篇文章的URI。然后，我们可以查询知识图谱，看看这些文章中哪些被标记了相关的MeSH术语。此外，我们保存这些文章的摘要，以便在下一步使用。这也是我们可以基于访问控制或其他用户控制参数（如作者、文件类型、发布日期等）进行过滤的地方。我在这个应用中没有包括这些内容，但我确实添加了访问控制和发布日期的属性，以防我们以后想在这个用户界面中添加这些功能。

下面是app.py中的代码样式：

```py
 if st.button("Filter Articles"):
            try:
                # Check if we have URIs from tab 1
                if "article_uris" in st.session_state and st.session_state.article_uris:
                    article_uris = st.session_state.article_uris

                    # Convert list of URIs into a string for the VALUES clause or FILTER
                    article_uris_string = ", ".join([f"<{str(uri)}>" for uri in article_uris])

                    SPARQL_QUERY = """
                    PREFIX schema: <http://schema.org/>
                    PREFIX ex: <http://example.org/>

                    SELECT ?article ?title ?abstract ?datePublished ?access ?meshTerm
                    WHERE {{
                      ?article a ex:Article ;
                               schema:name ?title ;
                               schema:description ?abstract ;
                               schema:datePublished ?datePublished ;
                               ex:access ?access ;
                               schema:about ?meshTerm .

                      ?meshTerm a ex:MeSHTerm .

                      FILTER (?article IN ({article_uris}))
                    }}
                    """
                    # Insert the article URIs into the query
                    query = SPARQL_QUERY.format(article_uris=article_uris_string)
                else:
                    st.write("No articles selected from Tab 1.")
                    st.stop()

                # Query the RDF and save results in session state
                top_articles = query_rdf(LOCAL_FILE_PATH, query, final_terms)
                st.session_state.filtered_articles = top_articles

                if top_articles:

                    # Combine abstracts from top articles and save in session state
                    def combine_abstracts(ranked_articles):
                        combined_text = " ".join(
                            [f"Title: {data['title']} Abstract: {data['abstract']}" for article_uri, data in
                             ranked_articles]
                        )
                        return combined_text

                    st.session_state.combined_text = combine_abstracts(top_articles)

                else:
                    st.write("No articles found for the selected terms.")
            except Exception as e:
                st.error(f"Error filtering articles: {e}")
```

这使用了rdf_queries.py文件中的query_rdf函数。该函数如下所示：

```py
# Function to query RDF using SPARQL
def query_rdf(local_file_path, query, mesh_terms, base_namespace="http://example.org/mesh/"):
    if not mesh_terms:
        raise ValueError("The list of MeSH terms is empty or invalid.")

    print("SPARQL Query:", query)

    # Create and parse the RDF graph
    g = Graph()
    g.parse(local_file_path, format="ttl")

    article_data = {}

    for term in mesh_terms:
        # Convert the term to a valid URI
        mesh_term_uri = convert_to_uri(term, base_namespace)
        #print("Term:", term, "URI:", mesh_term_uri)

        # Perform SPARQL query with initBindings
        results = g.query(query, initBindings={'meshTerm': mesh_term_uri})

        for row in results:
            article_uri = row['article']
            if article_uri not in article_data:
                article_data[article_uri] = {
                    'title': row['title'],
                    'abstract': row['abstract'],
                    'datePublished': row['datePublished'],
                    'access': row['access'],
                    'meshTerms': set()
                }
            article_data[article_uri]['meshTerms'].add(str(row['meshTerm']))
        #print("DEBUG article_data:", article_data)

    # Rank articles by the number of matching MeSH terms
    ranked_articles = sorted(
        article_data.items(),
        key=lambda item: len(item[1]['meshTerms']),
        reverse=True
    )
    return ranked_articles[:10]
```

如你所见，这个功能还会将MeSH术语转换为URI，这样我们就可以通过图形进行过滤。在将术语转换为URI时要小心，并确保它与其他功能一致。

下面是应用程序中的界面样式：

![](../Images/11971e4329e6ec965e8cf188e1aec1a1.png)

图片来自作者

如你所见，我们从前一步选择的两个MeSH术语已经列出。如果我点击“过滤文章”，它将使用我们在第2步中的过滤条件过滤原始的10篇文章。文章将返回其完整的摘要，以及标记的MeSH术语（见下图）。

![](../Images/5f45112e4ef1d0b014865f755e64ac2c.png)

图片来自作者

返回了5篇文章。其中两篇标记为“口腔肿瘤”，一篇标记为“牙龈肿瘤”，两篇标记为“腭肿瘤”。

现在我们已经有了一个精炼的文章列表，准备用来生成回应，我们可以进入最后一步。我们希望将这些文章发送给LLM生成回应，但我们也可以在提示中加入额外的上下文。我有一个默认的提示，内容是：“用要点总结这里的关键信息。使其对没有医学学位的人也能理解。”对于这个演示，我将调整提示以反映我们的原始搜索词：

![](../Images/ebb1666b6c3ca891803cb3d817272f05.png)

结果如下：

![](../Images/e55ff463f58f5f79fd3a82d1d842be3a.png)

结果看起来对我来说更好，主要是因为我知道我们总结的文章大概是关于口腔癌治疗的。这些数据集不包含实际的期刊文章，只有摘要。因此，这些结果只是摘要的摘要。这可能有一定价值，但如果我们在构建一个真正的应用程序，而不仅仅是一个演示的话，这是我们可以将文章的全文加入的步骤。或者，这也是用户/研究人员自己去阅读这些文章的地方，而不是完全依赖于LLM来进行总结。

# 结论

本教程演示了如何将向量数据库和知识图谱相结合，以显著提升RAG应用程序的效果。通过利用向量相似性进行初步搜索，结合结构化的知识图谱元数据进行筛选和组织，我们可以构建一个提供准确、可解释和领域特定结果的系统。MeSH（医学主题词表）的集成展示了领域专业知识在策划元数据中的重要性，它确保了检索步骤与应用程序的独特需求保持一致，同时与其他系统保持互操作性。这种方法不限于医学领域——其原理可以应用于任何结构化数据与文本信息共存的领域。

本教程强调了充分利用每种技术各自擅长领域的重要性。向量数据库擅长基于相似性的检索，而知识图谱则在提供上下文、结构和语义方面表现出色。此外，扩展RAG应用程序需要一个元数据层，以打破数据孤岛并实施治理策略。深思熟虑的设计，根植于特定领域的元数据和强有力的治理，是构建既准确又可扩展的RAG系统的关键路径。
