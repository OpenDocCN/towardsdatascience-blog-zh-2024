# 如何使用知识图谱和向量数据库实现图谱增强生成（Graph RAG）

> 原文：[https://towardsdatascience.com/how-to-implement-graph-rag-using-knowledge-graphs-and-vector-databases-60bb69a22759?source=collection_archive---------0-----------------------#2024-09-06](https://towardsdatascience.com/how-to-implement-graph-rag-using-knowledge-graphs-and-vector-databases-60bb69a22759?source=collection_archive---------0-----------------------#2024-09-06)

![](../Images/bb57df531853e6ba435c6662113afbce.png)

图片由作者提供

## 实现检索增强生成（RAG）、语义搜索和推荐的分步教程

[](https://stevehedden.medium.com/?source=post_page---byline--60bb69a22759--------------------------------)[![Steve Hedden](../Images/af7bec4a191ab857eccd885dd89e88b4.png)](https://stevehedden.medium.com/?source=post_page---byline--60bb69a22759--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--60bb69a22759--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--60bb69a22759--------------------------------) [Steve Hedden](https://stevehedden.medium.com/?source=post_page---byline--60bb69a22759--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--60bb69a22759--------------------------------) ·阅读时长 39 分钟·2024年9月6日

--

*本教程的相关代码可以在* [*这里找到。*](https://github.com/SteveHedden/kg_llm)

我的[上一篇博客](https://medium.com/towards-data-science/how-to-implement-knowledge-graphs-and-large-language-models-llms-together-at-the-enterprise-level-cf2835475c47)讲述了如何在企业级别上将知识图谱（KGs）和大型语言模型（LLMs）结合使用。在那篇文章中，我讨论了当前知识图谱和大型语言模型互动的两种方式：将大型语言模型作为工具来构建知识图谱；以及将知识图谱作为输入应用于大型语言模型或生成式AI应用程序。下面的图示展示了这两种集成方式及人们如何将它们结合使用的不同方式。

![](../Images/a8660e076ec5e3ba690aac08a47eceac.png)

图片由作者提供

在这篇文章中，我将聚焦于知识图谱和大语言模型（LLMs）共同使用的一种流行方式：使用知识图谱的 RAG，有时称为 [图谱 RAG](https://www.ontotext.com/knowledgehub/fundamentals/what-is-graph-rag/)、[GraphRAG](https://microsoft.github.io/graphrag/)、[GRAG](https://arxiv.org/abs/2405.16506) 或 [语义 RAG](https://www.poolparty.biz/semantic-retrieval-augmented-generation)。检索增强生成（RAG）是通过**检索**相关信息来**增强**发送给大语言模型（LLM）的提示，然后由 LLM **生成**响应。其核心思想是，与你直接将提示发送给没有经过你数据训练的 LLM 相比，你可以通过补充相关信息来增强提示，从而帮助 LLM 更准确地回答你的问题。我在之前的文章中举的例子是，将职位描述和我的简历复制到 ChatGPT 中，让它写一封求职信。如果我提供简历和我申请的职位描述，大语言模型将能为我的提示“写一封求职信”提供一个更相关的回答。由于知识图谱是专门用于存储知识的，因此它们是存储内部数据并通过额外上下文补充 LLM 提示的理想方式，从而提高回答的准确性和上下文理解。

重要的是，我认为常常被误解的一点是，RAG 和使用知识图谱的 RAG（图谱 RAG）是将技术结合起来的方案，而不是某种产品或技术本身。没有人发明、拥有或垄断图谱 RAG。大多数人能够看到这两项技术结合后所能带来的潜力，而且有越来越多的 [研究](https://arxiv.org/pdf/2311.07509) 和 [研究](https://arxiv.org/pdf/2405.11706) 证明了将它们结合的好处。

通常，使用知识图谱（KG）进行检索的 RAG（检索增强生成）有三种方式：

1.  **基于向量的检索：** 对你的知识图谱进行向量化并将其存储在向量数据库中。如果你将自然语言提示进行向量化，你可以在向量数据库中找到与提示最相似的向量。由于这些向量对应于图谱中的实体，你可以根据自然语言提示返回图谱中最“相关”的实体。*请注意，你可以在没有图谱的情况下进行基于向量的检索。这实际上是 RAG 最初实现的方式，有时称为基线 RAG。你可以对 SQL 数据库或内容进行向量化，并在查询时检索它。*

1.  **提示到查询的检索：** 使用大语言模型（LLM）为你编写一个 SPARQL 或 Cypher 查询，将该查询应用于你的知识图谱（KG），然后使用返回的结果来增强你的提示。

1.  **混合（向量 + SPARQL）：** 你可以将这两种方法以各种有趣的方式结合起来。在本教程中，我将演示一些你可以结合这些方法的方式。我将主要聚焦于使用向量化进行初步检索，然后使用 SPARQL 查询来精炼结果。

然而，有很多方法可以将向量数据库和KG结合起来进行搜索、相似性和RAG。这只是一个示例，用于突出每种方法的优缺点，以及它们结合使用的好处。我在这里使用它们的方式——初始检索时使用向量化，然后用SPARQL进行过滤——并不独特。我曾在其他地方看到过类似的实现。有一个来自大型家具制造商的例子，我曾听说过。他说，向量数据库可能会向购买沙发的人推荐一把衣物刷，但知识图谱会理解材料、属性和关系，并确保不会向购买皮沙发的人推荐衣物刷。

在本教程中，我将：

+   将数据集向量化为向量数据库，以测试语义搜索、相似性搜索和RAG *(基于向量的检索)*

+   将数据转化为KG以测试语义搜索、相似性搜索和RAG *(提示到查询检索，实际上更像是查询检索，因为我只是直接使用SPARQL，而不是让LLM将我的自然语言提示转化为SPARQL查询)*

+   将带有标签和URI的知识图谱数据集向量化为向量数据库（我将其称为“向量化知识图谱”），并测试语义搜索、相似性和RAG *(混合型)*

目标是阐明KG和向量数据库在这些功能上的差异，并展示它们如何协同工作。以下是向量数据库和知识图谱如何联合执行高级查询的高级概述。

![](../Images/bb57df531853e6ba435c6662113afbce.png)

图片由作者提供

如果你不想继续阅读，下面是总结：

+   向量数据库可以很好地进行语义搜索、相似性计算和一些基本的RAG操作，但也有一些前提条件。第一个前提是，我所使用的数据包含期刊文章的摘要，也就是说，它与相当大量的非结构化文本相关联。向量化模型主要针对非结构化数据进行训练，因此在处理与实体相关的文本片段时表现良好。

+   话虽如此，将数据导入向量数据库并准备好进行查询的开销非常小。如果你有一个包含一些非结构化数据的数据集，你可以在15分钟内完成向量化并开始搜索。

+   不出所料，单独使用向量数据库的最大缺点之一是缺乏可解释性。结果可能有三个很好的结果和一个不太合理的结果，但无法知道为什么这个第四个结果会出现。

+   向量数据库返回无关内容的几率对于搜索和相似性而言是一个麻烦，但对于RAG来说是一个巨大的问题。如果你在提示中增加了四篇文章，而其中一篇完全与主题无关，LLM的回应就会误导人。这通常被称为“上下文污染”。

+   上下文污染的特别危险之处在于，响应不一定在事实上不准确，而且它并不是基于错误的数据，而是使用了错误的数据来回答你的问题。我在本教程中找到的一个例子是关于提示词“口腔肿瘤的治疗方法”。其中一篇检索到的文章是关于直肠癌治疗的研究，并被发送到LLM进行总结。我不是医生，但我敢肯定直肠并不属于口腔的一部分。LLM准确地总结了该研究以及不同治疗方法对口腔癌和直肠癌的影响，但并不总是提到癌症类型。因此，用户在请求LLM描述口腔癌治疗方法后，会在不知情的情况下阅读到LLM描述直肠癌治疗方法的内容。

+   KG在语义搜索和相似性搜索中能够做得好与否，取决于元数据的质量以及元数据所连接的受控词汇表的质量。在本教程中的示例数据集中，期刊文章已经被标记了相关的主题词。这些词是一个丰富的受控词汇的一部分，即来自美国国立卫生研究院的[医学主题词](https://www.ncbi.nlm.nih.gov/mesh/)（MeSH）。因此，我们可以非常轻松地进行语义搜索和相似性搜索。

+   将KG直接向量化到向量数据库中，用作RAG的知识库，可能会带来一定的好处，但我在本教程中并没有这样做。我只是将数据以表格格式向量化，但为每篇文章添加了一列URI，以便将向量与KG连接起来。

+   使用KG进行语义搜索、相似性搜索和RAG的最大优势之一在于可解释性。你总是可以解释为什么某些结果被返回：它们被标记了某些概念或具有某些元数据属性。

+   我没有预料到KG的另一个好处是有时被称为“增强数据丰富”或“[图谱作为专家](https://www.ontotext.com/knowledgehub/fundamentals/what-is-graph-rag/)”——你可以使用KG来扩展或细化你的搜索词。例如，你可以找到类似的术语、更窄的术语，或以特定方式与搜索词相关的术语，从而扩展或细化你的查询。例如，我可能会从搜索“口腔癌”开始，但根据我的KG术语和关系，将搜索细化为“牙龈肿瘤和腭部肿瘤”。

+   使用知识图谱（KG）的最大障碍之一是你需要构建一个KG。话虽如此，有许多方法可以使用大型语言模型（LLM）加速KG的构建（见上图1）。

+   单独使用KG的一个缺点是你需要编写SPARQL查询才能完成所有操作。因此，上面提到的从提示到查询的检索方法才会受到欢迎。

+   使用Jaccard相似度在术语上找到类似文章的结果较差。如果没有特定的过滤，KG会返回一些具有重叠标签的文章，例如“老年”，“男性”和“人类”，这些标签可能远不如“治疗选项”或“口腔肿瘤”相关。

+   我遇到的另一个问题是，Jaccard相似度的计算需要很长时间（大约30分钟）。我不确定是否有更好的方法来实现这一点（欢迎提出建议），但我猜测，找到文章与9,999篇其他文章之间的重叠标签本身就是一种非常计算密集型的操作。

+   由于本教程中我使用的示例提示非常简单，比如“总结这些文章”，因此LLM的回答准确性（无论是基于向量的还是基于KG的检索方法）更多取决于检索结果，而不是生成过程。我的意思是，只要给LLM提供相关的上下文，它就不太可能在类似“总结”这样简单的提示上出错。当然，如果我们的提示更为复杂，这种情况会有所不同。

+   使用向量数据库进行初步搜索，再使用KG进行过滤，提供了最佳的结果。这是显而易见的——你不会过滤掉更差的结果。但这就是重点：并不是说KG本身一定能改善结果，而是KG为你提供了控制输出的能力，从而优化结果。

+   使用知识图谱（KG）过滤结果可以提高基于提示的准确性和相关性，但它也可以根据编写提示的人来定制结果。例如，我们可能希望使用相似度搜索来找到类似的文章并推荐给用户，但我们只希望推荐该用户可以访问的文章。KG允许在查询时进行访问控制。

+   知识图谱（KG）还可以帮助减少上下文污染的可能性。在上面的RAG示例中，我们可以在向量数据库中搜索“口腔肿瘤的治疗方法”，然后过滤出仅标记为口腔肿瘤（或相关概念）的文章。

+   在本教程中，我只关注了一个简单的实现方式，即直接将提示发送到向量数据库，然后使用图形过滤结果。实际上有更好的方法。例如，您可以从提示中提取与受控词汇对齐的实体，并通过图谱对它们进行丰富（包括同义词和更窄的术语）；您可以将提示解析成语义块，并将它们分别发送到向量数据库；您可以在向量化之前将RDF数据转化为文本，以便语言模型更好地理解等等。这些内容将是未来博客文章的主题。

# 第一步：基于向量的检索

下图展示了高层次的计划。我们希望将期刊文章的摘要和标题向量化到一个向量数据库中，以运行不同的查询：语义搜索、相似性搜索和RAG的简化版本。对于语义搜索，我们将测试一个术语，比如“口腔肿瘤”——向量数据库应该返回与此主题相关的文章。对于相似性搜索，我们将使用给定文章的ID来查找其在向量空间中的最近邻，即与该文章最相似的文章。最后，向量数据库允许一种形式的RAG，我们可以用一篇文章来补充一个提示，比如“请像对一个没有医学学位的人解释这篇文章一样”。

![](../Images/e466e1c2961bfa2db76099214cf93fe0.png)

作者提供的图片

我决定使用来自PubMed存储库的50,000篇研究文章的[这个](https://www.kaggle.com/datasets/owaiskhan9654/pubmed-multilabel-text-classification)数据集（许可证[CC0：公共领域](https://creativecommons.org/publicdomain/zero/1.0/))。这个数据集包含文章的标题、摘要以及元数据标签字段。这些标签来自医学主题词表（MeSH）受控词汇表。为了本部分教程的目的，我们将仅使用摘要和标题。原因是我们正在尝试将向量数据库与知识图谱进行比较，向量数据库的优势在于其能够“理解”没有丰富元数据的非结构化数据。我只使用了前10,000行数据，这样可以加快计算速度。

[这里](https://weaviate.io/developers/weaviate/quickstart)是Weaviate的官方快速入门教程。我还发现[这篇](/getting-started-with-weaviate-a-beginners-guide-to-search-with-vector-databases-14bbb9285839)文章对入门非常有帮助。

```py
from weaviate.util import generate_uuid5
import weaviate
import json
import pandas as pd

#Read in the pubmed data
df = pd.read_csv("PubMed Multi Label Text Classification Dataset Processed.csv")
```

然后我们可以建立与Weaviate集群的连接：

```py
client = weaviate.Client(
    url = "XXX",  # Replace with your Weaviate endpoint
    auth_client_secret=weaviate.auth.AuthApiKey(api_key="XXX"),  # Replace with your Weaviate instance API key
    additional_headers = {
        "X-OpenAI-Api-Key": "XXX"  # Replace with your inference API key
    }
)
```

在将数据向量化到向量数据库之前，我们必须定义模式。在这里，我们定义从csv中哪些列需要向量化。如前所述，本教程的目的，开始时我只希望向量化标题和摘要列。

```py
class_obj = {
    # Class definition
    "class": "articles",

    # Property definitions
    "properties": [
        {
            "name": "title",
            "dataType": ["text"],
        },
        {
            "name": "abstractText",
            "dataType": ["text"],
        },
    ],

    # Specify a vectorizer
    "vectorizer": "text2vec-openai",

    # Module settings
    "moduleConfig": {
        "text2vec-openai": {
            "vectorizeClassName": True,
            "model": "ada",
            "modelVersion": "002",
            "type": "text"
        },
        "qna-openai": {
          "model": "gpt-3.5-turbo-instruct"
        },
        "generative-openai": {
          "model": "gpt-3.5-turbo"
        }
    },
}
```

然后我们将这个模式推送到我们的Weaviate集群：

```py
client.schema.create_class(class_obj)
```

你可以通过直接查看你的Weaviate集群来确认这一点。

现在我们已经建立了模式，我们可以将所有数据写入向量数据库。

```py
import logging
import numpy as np

# Configure logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s %(levelname)s %(message)s')

# Replace infinity values with NaN and then fill NaN values
df.replace([np.inf, -np.inf], np.nan, inplace=True)
df.fillna('', inplace=True)

# Convert columns to string type
df['Title'] = df['Title'].astype(str)
df['abstractText'] = df['abstractText'].astype(str)

# Log the data types
logging.info(f"Title column type: {df['Title'].dtype}")
logging.info(f"abstractText column type: {df['abstractText'].dtype}")

with client.batch(
    batch_size=10,  # Specify batch size
    num_workers=2,   # Parallelize the process
) as batch:
    for index, row in df.iterrows():
        try:
            question_object = {
                "title": row.Title,
                "abstractText": row.abstractText,
            }
            batch.add_data_object(
                question_object,
                class_name="articles",
                uuid=generate_uuid5(question_object)
            )
        except Exception as e:
            logging.error(f"Error processing row {index}: {e}")
```

为了检查数据是否已经进入集群，你可以运行以下命令：

```py
client.query.aggregate("articles").with_meta_count().do()
```

不知为何，我的行数只有9997行被向量化。¯\_(ツ)_/¯

## 使用向量数据库进行语义搜索

当我们谈论向量数据库中的语义时，我们指的是通过大规模无结构内容训练的LLM API将术语向量化到向量空间中。这意味着向量会考虑术语的上下文。例如，如果在训练数据中，术语“马克·吐温”与术语“塞缪尔·克莱门斯”多次一起出现，那么这两个术语的向量应该在向量空间中相互接近。同样，如果“口腔癌”一词与“口腔肿瘤”一词在训练数据中多次一起出现，我们会期望关于口腔癌的文章的向量在向量空间中靠近关于口腔肿瘤的文章的向量。

你可以通过运行一个简单的查询来检查是否成功：

```py
response = (
    client.query
    .get("articles", ["title","abstractText"])
    .with_additional(["id"])
    .with_near_text({"concepts": ["Mouth Neoplasms"]})
    .with_limit(10)
    .do()
)

print(json.dumps(response, indent=4))
```

以下是搜索结果：

+   **文章 1：** *“牙龈转移作为表皮样恶性间皮瘤多脏器传播的首个信号。”* 这篇文章讲述的是一项研究，研究对象是那些患有恶性间皮瘤（肺癌的一种形式）并且癌症扩散到牙龈的患者。该研究旨在测试不同治疗方法（化疗、切除术和放疗）对癌症的影响。这似乎是一个合适的文章返回——它是关于牙龈肿瘤的，属于口腔肿瘤的一个子集。

+   **文章 2：** *“小腺体来源的肌上皮瘤。光学与电子显微镜研究。”* 这篇文章讲述的是一例14岁男孩的肿瘤，该肿瘤从他的牙龈移除，并且扩展到上颌的一部分，且该肿瘤的细胞来源于唾液腺。这个文章似乎也是一个合适的返回——它是关于从男孩的口腔中移除的肿瘤。

+   **文章 3：** *“下颌骨的转移性神经母细胞瘤。病例报告。”* 这篇文章是关于一名5岁男孩在下颌骨发现癌症的病例研究。这是关于癌症的，但技术上讲，它不是口腔癌——下颌肿瘤（下颌骨的肿瘤）不是口腔肿瘤的子集。

这就是我们所说的语义搜索——这些文章的标题或摘要中没有出现“口腔”这个词。第一篇文章是关于牙龈（口腔周围的组织）肿瘤的，它是口腔肿瘤的一种子集。第二篇文章讲述的是一种来源于口腔腺体的牙龈肿瘤，也是口腔肿瘤的子集。第三篇文章是关于下颌肿瘤的——从技术上讲，根据医学主题词（MeSH）词汇表，**这不是**口腔肿瘤的子集。然而，向量数据库知道，下颌骨靠近口腔。

## 使用向量数据库进行相似性搜索

我们还可以使用向量数据库来查找相似的文章。我选择了一篇之前通过口腔肿瘤查询返回的文章，标题为*“牙龈转移作为表皮样恶性间皮瘤多脏器传播的首个信号。”* 使用该文章的ID，我可以查询向量数据库中所有相似的实体：

```py
response = (
    client.query
    .get("articles", ["title", "abstractText"])
    .with_near_object({
        "id": "a7690f03-66b9-5d17-b765-8c6eb21f99c8" #id for a given article
    })
    .with_limit(10)
    .with_additional(["distance"])
    .do()
)

print(json.dumps(response, indent=2))
```

结果按相似度排名。相似度是通过向量空间中的距离计算的。正如你所看到的，排名第一的结果是牙龈文章——这篇文章与其自身最为相似。

其他文章包括：

+   **第4篇文章**：“针对烟草使用者筛查口腔恶性病变的可行性研究。”* 这篇文章讨论的是口腔癌，但重点是如何让吸烟者参与筛查，而不是他们的治疗方式。

+   **第5篇文章：** *“扩展性胸膜切除术和剥除术治疗恶性胸膜间皮瘤：老年人有效且安全的细胞减灭手术。”* 这篇文章是关于在老年人中用胸膜切除术和剥除术（手术去除肺部癌症）治疗胸膜间皮瘤（肺部癌症）的一项研究。因此，它在治疗恶性间皮瘤方面类似，但与牙龈肿瘤无关。

+   **第3篇文章（如上所述）：** *“下颌骨转移性神经母细胞瘤。病例报告。”* 这篇文章讲的是一名5岁男孩下颌骨患癌的案例。虽然它讨论的是癌症，但技术上来说并不是口腔癌，并且它也不像牙龈文章那样聚焦于治疗结果。

可以说，所有这些文章与我们最初的牙龈文章都很相似。很难评估它们的相似度，因此也很难评估相似度搜索的效果，因为这在很大程度上取决于用户对“相似”的定义。你是否对其他关于间皮瘤治疗的文章感兴趣，并且认为第一篇文章关于其如何扩展到牙龈的部分无关紧要？如果是那样的话，**第5篇文章**是最相似的。还是你对任何类型的口腔癌感兴趣，不论是通过治疗还是预防？如果是那样的话，**第4篇文章**是最相似的。向量数据库的一个缺点是，它是一个黑箱——我们无法知道这些文章为何会被返回。

## 使用向量数据库的检索增强生成（RAG）

下面是如何使用向量数据库检索结果，并将其发送给大语言模型（LLM）进行总结的过程——这是RAG的一个示例。

```py
response = (
    client.query
    .get("articles", ["title", "abstractText"])
    .with_near_text({"concepts": ["Gingival metastasis as first sign of multiorgan dissemination of epithelioid malignant mesothelioma"]})
    .with_generate(single_prompt="Please explain this article {title} like you would to someone without a medical degree.")
    .with_limit(1)
    .do()
)

print(json.dumps(response, indent=4))
```

你可以看到以下的响应：

> “当然！这篇文章讲述了一个案例，一名患者患有一种叫做上皮型恶性间皮瘤的癌症。该癌症通常起源于肺部或腹部的衬里。然而，在这种情况下，癌症扩散到身体其他部位的第一个迹象是在牙龈（牙龈）中发现。这被称为牙龈转移。\n\n转移是指癌细胞从原发肿瘤扩散到身体其他部位。在这个案例中，癌症首先扩散到牙龈，然后才扩散到其他器官。这很重要，因为它表明癌症已经很晚期，并且在被发现之前已经扩散到多个器官。\n\n总体而言，这篇文章强调了早期发现和癌症监测的重要性，以及癌症可能扩散到身体意想不到部位的潜力。”

我实际上对这个回应感到失望。摘要明确解释了这是一个跟踪13名接受不同治疗的恶性间皮瘤转移患者及其结果的研究。RAG的输出将文章描述为“一个人”，根本没有提到这项研究。

与其仅仅总结一篇文章，不如尝试总结几篇。在这个例子中，我们使用与上面相同的搜索词（口腔肿瘤），然后将前三篇文章连同一个提示发送给LLM，提示内容是：“以要点形式总结这里的关键信息。让没有医学学位的人也能理解。”

```py
 response = (
    client.query
    .get(collection_name, ["title", "abstractText"])
    .with_near_text({"concepts": ["Mouth Neoplasms"]})
    .with_limit(3)
    .with_generate(grouped_task="Summarize the key information here in bullet points. Make it understandable to someone without a medical degree.")
    .do()
)

print(response["data"]["Get"]["Articles"][0]["_additional"]["generate"]["groupedResult"])
```

这是结果：

```py
- Metastatic malignant mesothelioma to the oral cavity is rare, with more cases in jaw bones than soft tissue
- Average survival rate for this type of cancer is 9-12 months
- Study of 13 patients who underwent neoadjuvant chemotherapy and surgery showed a median survival of 11 months
- One patient had a gingival mass as the first sign of multiorgan recurrence of mesothelioma
- Biopsy of new growing lesions, even in uncommon sites, is important for patients with a history of mesothelioma
- Myoepithelioma of minor salivary gland origin can show features indicative of malignant potential
- Metastatic neuroblastoma in the mandible is very rare and can present with osteolytic jaw defects and looseness of deciduous molars in children
```

对我来说，这比之前的回应看起来更好——它提到了**文章1**中进行的研究、治疗和结果。倒数第二个要点是关于*“小型唾液腺来源的肌上皮瘤。光学和电子显微镜研究”*的文章，似乎是一个准确的一行描述。最后一个要点是关于上面提到的**文章3**，再次看起来是一个准确的一行描述。

# 步骤2：使用知识图谱进行数据检索

下面是我们如何使用知识图谱进行语义搜索、相似性搜索和RAG的高级概览：

![](../Images/7625e519cdfa5e2d9290ba765ec43526.png)

图片由作者提供

使用知识图谱检索数据的第一步是将数据转换为RDF格式。下面的代码为所有数据类型创建类和属性，然后使用文章和MeSH术语的实例填充数据。我还创建了发布日期和访问级别的属性，并用随机值填充它们，作为演示。

```py
from rdflib import Graph, RDF, RDFS, Namespace, URIRef, Literal
from rdflib.namespace import SKOS, XSD
import pandas as pd
import urllib.parse
import random
from datetime import datetime, timedelta

# Create a new RDF graph
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
    return [term.strip().replace(' ', '_') for term in mesh_list.strip("[]'").split(',')]

# Function to create a valid URI
def create_valid_uri(base_uri, text):
    if pd.isna(text):
        return None
    sanitized_text = urllib.parse.quote(text.strip().replace(' ', '_').replace('"', '').replace('<', '').replace('>', '').replace("'", "_"))
    return URIRef(f"{base_uri}/{sanitized_text}")

# Function to generate a random date within the last 5 years
def generate_random_date():
    start_date = datetime.now() - timedelta(days=5*365)
    random_days = random.randint(0, 5*365)
    return start_date + timedelta(days=random_days)

# Function to generate a random access value between 1 and 10
def generate_random_access():
    return random.randint(1, 10)

# Load your DataFrame here
# df = pd.read_csv('your_data.csv')

# Loop through each row in the DataFrame and create RDF triples
for index, row in df.iterrows():
    article_uri = create_valid_uri("http://example.org/article", row['Title'])
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
        term_uri = create_valid_uri("http://example.org/mesh", term)
        if term_uri is None:
            continue

        # Add MeSH Term instance
        g.add((term_uri, RDF.type, MeSHTerm))
        g.add((term_uri, RDFS.label, Literal(term.replace('_', ' '), datatype=XSD.string)))

        # Link Article to MeSH Term
        g.add((article_uri, schema.about, term_uri))

# Serialize the graph to a file (optional)
g.serialize(destination='ontology.ttl', format='turtle')
```

## 使用知识图谱进行语义搜索

现在我们可以测试语义搜索。然而，“语义”这个词在知识图谱的语境中略有不同。在知识图谱中，我们依赖于与文档关联的标签以及它们在MeSH分类法中的关系来表示语义。例如，一篇文章可能是关于唾液腺肿瘤（唾液腺的癌症），但仍然被标记为“口腔肿瘤”。

我们不仅查询所有标记为“口腔肿瘤”的文章，还会查找任何比“口腔肿瘤”更狭义的概念。MeSH词汇表包含术语的定义，也包含类似“更广义”和“更狭义”的关系。

```py
from SPARQLWrapper import SPARQLWrapper, JSON

def get_concept_triples_for_term(term):
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

    sparql.setQuery(query)
    sparql.setReturnFormat(JSON)
    results = sparql.query().convert()

    triples = set()  # Using a set to avoid duplicate entries
    for result in results["results"]["bindings"]:
        obj_label = result.get("oLabel", {}).get("value", "No label")
        triples.add(obj_label)

    # Add the term itself to the list
    triples.add(term)

    return list(triples)  # Convert back to a list for easier handling

def get_narrower_concepts_for_term(term):
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

    sparql.setQuery(query)
    sparql.setReturnFormat(JSON)
    results = sparql.query().convert()

    concepts = set()  # Using a set to avoid duplicate entries
    for result in results["results"]["bindings"]:
        subject_label = result.get("narrowerConceptLabel", {}).get("value", "No label")
        concepts.add(subject_label)

    return list(concepts)  # Convert back to a list for easier handling

def get_all_narrower_concepts(term, depth=2, current_depth=1):
    # Create a dictionary to store the terms and their narrower concepts
    all_concepts = {}

    # Initial fetch for the primary term
    narrower_concepts = get_narrower_concepts_for_term(term)
    all_concepts[term] = narrower_concepts

    # If the current depth is less than the desired depth, fetch narrower concepts recursively
    if current_depth < depth:
        for concept in narrower_concepts:
            # Recursive call to fetch narrower concepts for the current concept
            child_concepts = get_all_narrower_concepts(concept, depth, current_depth + 1)
            all_concepts.update(child_concepts)

    return all_concepts

# Fetch alternative names and narrower concepts
term = "Mouth Neoplasms"
alternative_names = get_concept_triples_for_term(term)
all_concepts = get_all_narrower_concepts(term, depth=2)  # Adjust depth as needed

# Output alternative names
print("Alternative names:", alternative_names)
print()

# Output narrower concepts
for broader, narrower in all_concepts.items():
    print(f"Broader concept: {broader}")
    print(f"Narrower concepts: {narrower}")
    print("---")
```

以下是“口腔肿瘤”的所有替代名称和更狭义的概念。

![](../Images/1c30892d8e9629891ef3cf1da34f904c.png)

作者提供的图片

我们将其转换为一个平面术语列表：

```py
def flatten_concepts(concepts_dict):
    flat_list = []

    def recurse_terms(term_dict):
        for term, narrower_terms in term_dict.items():
            flat_list.append(term)
            if narrower_terms:
                recurse_terms(dict.fromkeys(narrower_terms, []))  # Use an empty dict to recurse

    recurse_terms(concepts_dict)
    return flat_list

# Flatten the concepts dictionary
flat_list = flatten_concepts(all_concepts)
```

然后我们将这些术语转化为MeSH URI，以便将其纳入我们的SPARQL查询中：

```py
#Convert the MeSH terms to URI
def convert_to_mesh_uri(term):
    formatted_term = term.replace(" ", "_").replace(",", "_").replace("-", "_")
    return URIRef(f"http://example.org/mesh/_{formatted_term}_")

# Convert terms to URIs
mesh_terms = [convert_to_mesh_uri(term) for term in flat_list]
```

然后我们编写一个SPARQL查询，查找所有标记为“口腔肿瘤”、其替代名称“口腔癌”或任何更狭义术语的文章：

```py
from rdflib import URIRef

query = """
PREFIX schema: <http://schema.org/>
PREFIX ex: <http://example.org/>

SELECT ?article ?title ?abstract ?datePublished ?access ?meshTerm
WHERE {
  ?article a ex:Article ;
           schema:name ?title ;
           schema:description ?abstract ;
           schema:datePublished ?datePublished ;
           ex:access ?access ;
           schema:about ?meshTerm .

  ?meshTerm a ex:MeSHTerm .
}
"""

# Dictionary to store articles and their associated MeSH terms
article_data = {}

# Run the query for each MeSH term
for mesh_term in mesh_terms:
    results = g.query(query, initBindings={'meshTerm': mesh_term})

    # Process results
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

        # Add the MeSH term to the set for this article
        article_data[article_uri]['meshTerms'].add(str(row['meshTerm']))

# Rank articles by the number of matching MeSH terms
ranked_articles = sorted(
    article_data.items(),
    key=lambda item: len(item[1]['meshTerms']),
    reverse=True
)

# Get the top 3 articles
top_3_articles = ranked_articles[:3]

# Output results
for article_uri, data in top_3_articles:
    print(f"Title: {data['title']}")
    print("MeSH Terms:")
    for mesh_term in data['meshTerms']:
        print(f"  - {mesh_term}")
    print()
```

返回的文章包括：

+   **第二篇文章（见上文）：** *“小唾液腺来源的肌上皮瘤。光学显微镜和电子显微镜研究。”*

+   **第四篇文章（见上文）：** “针对烟草使用者筛查口腔恶性病变的可行性研究。”

+   **第六篇文章：** *“胚胎致死异常视蛋白HuR与口腔鳞状细胞癌中环氧合酶-2表达的关系。”* 这篇文章是一项研究，旨在确定名为HuR的蛋白质是否与环氧合酶-2的高水平有关，后者在癌症的发展和癌细胞的扩散中起着作用。具体来说，这项研究关注的是口腔鳞状细胞癌，这是一种口腔癌。

这些结果与我们从向量数据库中获得的结果相似。每一篇文章都涉及口腔肿瘤。知识图谱方法的优点是我们能够获得可解释性——我们确切知道为什么这些文章被选中。文章2标记了“牙龈肿瘤”和“唾液腺肿瘤”。文章4和6都标记了“口腔肿瘤”。由于文章2标记了我们搜索术语中的两个匹配术语，它排名最高。

## 使用知识图谱进行相似性搜索

与其使用向量空间查找相似文章，我们可以依赖与文章相关联的标签。使用标签进行相似性匹配有多种方法，但在这个例子中，我将使用一种常见的方法：Jaccard相似度。我们将再次使用牙龈文章来进行不同方法之间的比较。

```py
from rdflib import Graph, URIRef
from rdflib.namespace import RDF, RDFS, Namespace, SKOS
import urllib.parse

# Define namespaces
schema = Namespace('http://schema.org/')
ex = Namespace('http://example.org/')
rdfs = Namespace('http://www.w3.org/2000/01/rdf-schema#')

# Function to calculate Jaccard similarity and return overlapping terms
def jaccard_similarity(set1, set2):
    intersection = set1.intersection(set2)
    union = set1.union(set2)
    similarity = len(intersection) / len(union) if len(union) != 0 else 0
    return similarity, intersection

# Load the RDF graph
g = Graph()
g.parse('ontology.ttl', format='turtle')

def get_article_uri(title):
    # Convert the title to a URI-safe string
    safe_title = urllib.parse.quote(title.replace(" ", "_"))
    return URIRef(f"http://example.org/article/{safe_title}")

def get_mesh_terms(article_uri):
    query = """
    PREFIX schema: <http://schema.org/>
    PREFIX ex: <http://example.org/>
    PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

    SELECT ?meshTerm
    WHERE {
      ?article schema:about ?meshTerm .
      ?meshTerm a ex:MeSHTerm .
      FILTER (?article = <""" + str(article_uri) + """>)
    }
    """
    results = g.query(query)
    mesh_terms = {str(row['meshTerm']) for row in results}
    return mesh_terms

def find_similar_articles(title):
    article_uri = get_article_uri(title)
    mesh_terms_given_article = get_mesh_terms(article_uri)

    # Query all articles and their MeSH terms
    query = """
    PREFIX schema: <http://schema.org/>
    PREFIX ex: <http://example.org/>
    PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

    SELECT ?article ?meshTerm
    WHERE {
      ?article a ex:Article ;
               schema:about ?meshTerm .
      ?meshTerm a ex:MeSHTerm .
    }
    """
    results = g.query(query)

    mesh_terms_other_articles = {}
    for row in results:
        article = str(row['article'])
        mesh_term = str(row['meshTerm'])
        if article not in mesh_terms_other_articles:
            mesh_terms_other_articles[article] = set()
        mesh_terms_other_articles[article].add(mesh_term)

    # Calculate Jaccard similarity
    similarities = {}
    overlapping_terms = {}
    for article, mesh_terms in mesh_terms_other_articles.items():
        if article != str(article_uri):
            similarity, overlap = jaccard_similarity(mesh_terms_given_article, mesh_terms)
            similarities[article] = similarity
            overlapping_terms[article] = overlap

    # Sort by similarity and get top 5
    top_similar_articles = sorted(similarities.items(), key=lambda x: x[1], reverse=True)[:15]

    # Print results
    print(f"Top 15 articles similar to '{title}':")
    for article, similarity in top_similar_articles:
        print(f"Article URI: {article}")
        print(f"Jaccard Similarity: {similarity:.4f}")
        print(f"Overlapping MeSH Terms: {overlapping_terms[article]}")
        print()

# Example usage
article_title = "Gingival metastasis as first sign of multiorgan dissemination of epithelioid malignant mesothelioma."
find_similar_articles(article_title)
```

结果如下。由于我们再次搜索了关于牙龈的文章，它是最相似的文章，这也是我们预期的。其他结果包括：

+   **第七篇文章：** *“股外侧肌的钙化性肌腱炎。三例报告。”* 这篇文章讲述的是股外侧肌（大腿的肌肉）中的钙化性肌腱炎（肌腱中钙质沉积）。这与口腔肿瘤无关。

+   **重叠术语：** 断层扫描、老年、男性、人类、X射线计算机断层扫描

+   **文章8：** *“前列腺癌患者在出现前列腺特异性抗原水平时，雄激素剥夺治疗的最佳持续时间是什么？”* 这篇文章讨论了前列腺癌患者应该接受多长时间的特定治疗（雄激素剥夺治疗）。这是一种癌症治疗（放射疗法），但与口腔癌无关。

+   **重叠术语：** 放射疗法、老年、男性、人类、辅助治疗

+   **文章9：** *CT扫描脑半球不对称性：预测失语症恢复的因素*。这篇文章讨论了大脑左右半球的差异（脑半球不对称性）如何预测中风后失语症患者的恢复情况。

+   **重叠术语：** 断层扫描、老年、男性、人类、X射线计算机断层扫描

这种方法最好的部分是，由于我们在这里计算相似度的方式，我们可以看到为什么其他文章是相似的——我们可以准确地看到哪些术语是重叠的，即哪些术语在牙龈文章和每篇对比文章中是共同的。

可解释性的缺点是，我们可以看到这些文章似乎不是最相似的，考虑到之前的结果。它们都在三个术语（老年、男性和人类）上有共同之处，但这些术语可能远没有“治疗方案”或“口腔肿瘤”那么相关。你可以重新计算，基于术语在语料库中的出现频率来加权——即“词频-逆文档频率”（TF-IDF），这可能会改善结果。你也可以选择在进行相似性计算时最相关的标记术语，从而对结果进行更多控制。

使用Jaccard相似性计算知识图谱中术语相似度的最大缺点是计算工作量——运行这个计算大约花了30分钟。

## 使用知识图谱的RAG

我们也可以仅使用知识图谱来进行RAG的检索部分。我们已经有了一份关于口腔肿瘤的文章列表，这些文章是从上述语义搜索中保存下来的结果。要实现RAG，我们只需要将这些文章发送给LLM，并要求它总结这些结果。

首先，我们将每篇文章的标题和摘要合并成一个大的文本块，称为combined_text：

```py
# Function to combine titles and abstracts
def combine_abstracts(top_3_articles):
    combined_text = "".join(
        [f"Title: {data['title']} Abstract: {data['abstract']}" for article_uri, data in top_3_articles]
    )
    return combined_text

# Combine abstracts from the top 3 articles
combined_text = combine_abstracts(top_3_articles)
print(combined_text)
```

然后我们设置一个客户端，以便可以将这个文本直接发送给LLM：

```py
import openai

# Set up your OpenAI API key
api_key = "YOUR API KEY"
openai.api_key = api_key
```

然后我们将上下文和提示发送给LLM：

```py
def generate_summary(combined_text):
    response = openai.Completion.create(
        model="gpt-3.5-turbo-instruct",
        prompt=f"Summarize the key information here in bullet points. Make it understandable to someone without a medical degree:\n\n{combined_text}",
        max_tokens=1000,
        temperature=0.3
    )

    # Get the raw text output
    raw_summary = response.choices[0].text.strip()

    # Split the text into lines and clean up whitespace
    lines = raw_summary.split('\n')
    lines = [line.strip() for line in lines if line.strip()]

    # Join the lines back together with actual line breaks
    formatted_summary = '\n'.join(lines)

    return formatted_summary

# Generate and print the summary
summary = generate_summary(combined_text)
print(summary)
```

结果如下所示：

```py
- A 14-year-old boy had a gingival tumor in his anterior maxilla that was removed and studied by light and electron microscopy
- The tumor was made up of myoepithelial cells and appeared to be malignant
- Electron microscopy showed that the tumor originated from a salivary gland
- This is the only confirmed case of a myoepithelioma with features of malignancy
- A feasibility study was conducted to improve early detection of oral cancer and premalignant lesions in a high incidence region
- Tobacco vendors were involved in distributing flyers to invite smokers for free examinations by general practitioners
- 93 patients were included in the study and 27% were referred to a specialist
- 63.6% of those referred actually saw a specialist and 15.3% were confirmed to have a premalignant lesion
- A study found a correlation between increased expression of the protein HuR and the enzyme COX-2 in oral squamous cell carcinoma (OSCC)
- Cytoplasmic HuR expression was associated with COX-2 expression and lymph node and distant metastasis in OSCCs
- Inhibition of HuR expression led to a decrease in COX-2 expression in oral cancer cells.
```

结果看起来不错，即它很好地总结了从语义搜索返回的三篇文章。仅使用知识图谱（KG）的RAG应用程序的响应质量取决于你的知识图谱检索相关文档的能力。正如这个例子所示，如果你的提示足够简单，比如“总结这里的关键信息”，那么难点就在于检索（将正确的文章作为上下文提供给LLM），而不是生成响应。

# 步骤3：使用向量化知识图谱测试数据检索

现在我们要联合各方力量。我们将在数据库中的每篇文章添加一个 URI，然后在 Weaviate 中创建一个新的集合，将文章的名称、摘要、相关的 MeSH 术语以及 URI 向量化。URI 是文章的唯一标识符，是我们将其与知识图谱连接的方式。

首先，我们在数据中为 URI 添加了一个新列：

```py
# Function to create a valid URI
def create_valid_uri(base_uri, text):
    if pd.isna(text):
        return None
    # Encode text to be used in URI
    sanitized_text = urllib.parse.quote(text.strip().replace(' ', '_').replace('"', '').replace('<', '').replace('>', '').replace("'", "_"))
    return URIRef(f"{base_uri}/{sanitized_text}")

# Add a new column to the DataFrame for the article URIs
df['Article_URI'] = df['Title'].apply(lambda title: create_valid_uri("http://example.org/article", title)) 
```

现在我们为新集合创建一个包含附加字段的新架构：

```py
class_obj = {
    # Class definition
    "class": "articles_with_abstracts_and_URIs",

    # Property definitions
    "properties": [
        {
            "name": "title",
            "dataType": ["text"],
        },
        {
            "name": "abstractText",
            "dataType": ["text"],
        },
        {
            "name": "meshMajor",
            "dataType": ["text"],
        },
        {
            "name": "Article_URI",
            "dataType": ["text"],
        },
    ],

    # Specify a vectorizer
    "vectorizer": "text2vec-openai",

    # Module settings
    "moduleConfig": {
        "text2vec-openai": {
            "vectorizeClassName": True,
            "model": "ada",
            "modelVersion": "002",
            "type": "text"
        },
        "qna-openai": {
          "model": "gpt-3.5-turbo-instruct"
        },
        "generative-openai": {
          "model": "gpt-3.5-turbo"
        }
    },
}
```

将该架构推送到向量数据库：

```py
client.schema.create_class(class_obj)
```

现在我们将数据向量化到新集合中：

```py
import logging
import numpy as np

# Configure logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s %(levelname)s %(message)s')

# Replace infinity values with NaN and then fill NaN values
df.replace([np.inf, -np.inf], np.nan, inplace=True)
df.fillna('', inplace=True)

# Convert columns to string type
df['Title'] = df['Title'].astype(str)
df['abstractText'] = df['abstractText'].astype(str)
df['meshMajor'] = df['meshMajor'].astype(str)
df['Article_URI'] = df['Article_URI'].astype(str)

# Log the data types
logging.info(f"Title column type: {df['Title'].dtype}")
logging.info(f"abstractText column type: {df['abstractText'].dtype}")
logging.info(f"meshMajor column type: {df['meshMajor'].dtype}")
logging.info(f"Article_URI column type: {df['Article_URI'].dtype}")

with client.batch(
    batch_size=10,  # Specify batch size
    num_workers=2,   # Parallelize the process
) as batch:
    for index, row in df.iterrows():
        try:
            question_object = {
                "title": row.Title,
                "abstractText": row.abstractText,
                "meshMajor": row.meshMajor,
                "article_URI": row.Article_URI,
            }
            batch.add_data_object(
                question_object,
                class_name="articles_with_abstracts_and_URIs",
                uuid=generate_uuid5(question_object)
            )
        except Exception as e:
            logging.error(f"Error processing row {index}: {e}")
```

## 使用向量化的知识图谱进行语义搜索

现在我们可以像之前一样在向量数据库上进行语义搜索，但具有更好的可解释性和对结果的控制。

```py
response = (
    client.query
    .get("articles_with_abstracts_and_URIs", ["title","abstractText","meshMajor","article_URI"])
    .with_additional(["id"])
    .with_near_text({"concepts": ["mouth neoplasms"]})
    .with_limit(10)
    .do()
)

print(json.dumps(response, indent=4))
```

结果是：

+   **文章 1：** "牙龈转移作为上皮样恶性间皮瘤多脏器扩散的首个迹象。"

+   **文章 10：** *"血管中心性中央面部淋巴瘤：一位老年男性的挑战性诊断。"* 这篇文章讲述了如何诊断一名鼻癌患者。

+   **文章 11：** *"下颌假性癌性增生。"* 这篇文章对我来说非常难以理解，但我认为它讨论了假性癌性增生如何看起来像癌症（因此名称中有“假性”一词），但实际上并不是癌症。虽然它似乎确实与下颌有关，但它被标记为 MeSH 术语“口腔肿瘤”。

很难说这些结果比单独使用知识图谱或向量数据库的结果更好还是更差。从理论上讲，结果应该更好，因为每篇文章相关的 MeSH 术语现在和文章一起被向量化。然而，我们并没有真正对知识图谱进行向量化。例如，MeSH 术语之间的关系并没有被包含在向量数据库中。

拥有 MeSH 术语向量化的好处是，可以立即得到一些可解释性——例如，文章 11 也标记了“口腔肿瘤”这一 MeSH 术语。但将向量数据库与知识图谱连接的真正酷的地方在于，我们可以从知识图谱中应用任何我们想要的过滤器。记得我们之前在数据中添加了“发布日期”作为字段吗？现在我们可以基于此进行过滤。假设我们想要找到 2020 年 5 月 1 日之后发布的关于口腔肿瘤的文章：

```py
from rdflib import Graph, Namespace, URIRef, Literal
from rdflib.namespace import RDF, RDFS, XSD

# Define namespaces
schema = Namespace('http://schema.org/')
ex = Namespace('http://example.org/')
rdfs = Namespace('http://www.w3.org/2000/01/rdf-schema#')
xsd = Namespace('http://www.w3.org/2001/XMLSchema#')

def get_articles_after_date(graph, article_uris, date_cutoff):
    # Create a dictionary to store results for each URI
    results_dict = {}

    # Define the SPARQL query using a list of article URIs and a date filter
    uris_str = " ".join(f"<{uri}>" for uri in article_uris)
    query = f"""
    PREFIX schema: <http://schema.org/>
    PREFIX ex: <http://example.org/>
    PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
    PREFIX xsd: <http://www.w3.org/2001/XMLSchema#>

    SELECT ?article ?title ?datePublished
    WHERE {{
      VALUES ?article {{ {uris_str} }}

      ?article a ex:Article ;
               schema:name ?title ;
               schema:datePublished ?datePublished .

      FILTER (?datePublished > "{date_cutoff}"^^xsd:date)
    }}
    """

    # Execute the query
    results = graph.query(query)

    # Extract the details for each article
    for row in results:
        article_uri = str(row['article'])
        results_dict[article_uri] = {
            'title': str(row['title']),
            'date_published': str(row['datePublished'])
        }

    return results_dict

date_cutoff = "2023-01-01"
articles_after_date = get_articles_after_date(g, article_uris, date_cutoff)

# Output the results
for uri, details in articles_after_date.items():
    print(f"Article URI: {uri}")
    print(f"Title: {details['title']}")
    print(f"Date Published: {details['date_published']}")
    print()
```

原始查询返回了十个结果（我们设置的最大值是十个），但其中只有六篇是在 2023 年 1 月 1 日之后发布的。请参见下面的结果：

![](../Images/ada8d32d499aa914c3251e746b68c4c5.png)

图片来源：作者

## 使用向量化的知识图谱进行相似度搜索

我们可以像之前对我们的牙龈文章（文章 1）所做的那样，在这个新集合上运行相似度搜索：

```py
response = (
    client.query
    .get("articles_with_abstracts_and_URIs", ["title","abstractText","meshMajor","article_URI"])
    .with_near_object({
        "id": "37b695c4-5b80-5f44-a710-e84abb46bc22"
    })
    .with_limit(50)
    .with_additional(["distance"])
    .do()
)

print(json.dumps(response, indent=2))
```

结果如下：

+   **文章 3：** *"下颌的转移性神经母细胞瘤。案例报告。"*

+   **文章 4：** *“针对烟草使用者进行口腔恶性病变筛查的可行性研究。”*

+   **第12篇文章：** *“弥漫性肺内恶性间皮瘤伪装为间质性肺病：一种独特的间皮瘤变异。”* 本文讲述了五名男性患者患有一种间皮瘤，这种间皮瘤看起来像另一种肺病：间质性肺病。

由于我们已经对MeSH标签进行了向量化处理，因此可以查看与每篇文章关联的标签。其中一些文章虽然在某些方面可能相似，但并非关于口腔肿瘤。假设我们希望找到与我们的牙龈文章相似的文章，但特别是关于口腔肿瘤的。现在我们可以将之前在知识图谱中应用的SPARQL过滤器与这些结果结合使用。

口腔肿瘤的同义词和更窄概念的MeSH URI已经保存，但仍需要为通过向量搜索返回的50篇文章提供URI：

```py
# Assuming response is the data structure with your articles
article_uris = [URIRef(article["article_URI"]) for article in response["data"]["Get"]["Articles_with_abstracts_and_URIs"]]
```

现在我们可以根据标签对结果进行排名，就像我们之前使用知识图谱进行语义搜索时那样。

```py
from rdflib import URIRef

# Constructing the SPARQL query with a FILTER for the article URIs
query = """
PREFIX schema: <http://schema.org/>
PREFIX ex: <http://example.org/>

SELECT ?article ?title ?abstract ?datePublished ?access ?meshTerm
WHERE {
  ?article a ex:Article ;
           schema:name ?title ;
           schema:description ?abstract ;
           schema:datePublished ?datePublished ;
           ex:access ?access ;
           schema:about ?meshTerm .

  ?meshTerm a ex:MeSHTerm .

  # Filter to include only articles from the list of URIs
  FILTER (?article IN (%s))
}
"""

# Convert the list of URIRefs into a string suitable for SPARQL
article_uris_string = ", ".join([f"<{str(uri)}>" for uri in article_uris])

# Insert the article URIs into the query
query = query % article_uris_string

# Dictionary to store articles and their associated MeSH terms
article_data = {}

# Run the query for each MeSH term
for mesh_term in mesh_terms:
    results = g.query(query, initBindings={'meshTerm': mesh_term})

    # Process results
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

        # Add the MeSH term to the set for this article
        article_data[article_uri]['meshTerms'].add(str(row['meshTerm']))

# Rank articles by the number of matching MeSH terms
ranked_articles = sorted(
    article_data.items(),
    key=lambda item: len(item[1]['meshTerms']),
    reverse=True
)

# Output results
for article_uri, data in ranked_articles:
    print(f"Title: {data['title']}")
    print(f"Abstract: {data['abstract']}")
    print("MeSH Terms:")
    for mesh_term in data['meshTerms']:
        print(f"  - {mesh_term}")
    print()
```

在原本由向量数据库返回的50篇文章中，只有5篇标记为口腔肿瘤或相关概念。

+   **第2篇文章：** *“小唾液腺起源的肌上皮瘤。光学和电子显微镜研究。”* 标签：牙龈肿瘤、唾液腺肿瘤

+   **第4篇文章：** *“针对烟草用户的口腔恶性病变筛查可行性研究。”* 标签：口腔肿瘤

+   **第13篇文章：** *“起源于牙龈沟的表皮样癌。”* 本文描述了一个牙龈癌的病例（牙龈肿瘤）。标签：牙龈肿瘤

+   **第1篇文章：** *“****牙龈转移作为上皮样恶性间皮瘤多脏器扩散的首个征兆。”* 标签：牙龈肿瘤

+   **第14篇文章：** *“腮腺淋巴结转移：CT和MR影像学发现。”* 本文讲述了腮腺肿瘤，即主要的唾液腺肿瘤。标签：腮腺肿瘤

最后，假设我们希望向用户推荐这些相似的文章，但我们只想推荐该用户可以访问的文章。假设我们知道该用户只能访问标记为访问级别3、5和7的文章。我们可以在知识图谱中使用类似的SPARQL查询应用过滤器：

```py
from rdflib import Graph, Namespace, URIRef, Literal
from rdflib.namespace import RDF, RDFS, XSD, SKOS

# Assuming your RDF graph (g) is already loaded

# Define namespaces
schema = Namespace('http://schema.org/')
ex = Namespace('http://example.org/')
rdfs = Namespace('http://www.w3.org/2000/01/rdf-schema#')

def filter_articles_by_access(graph, article_uris, access_values):
    # Construct the SPARQL query with a dynamic VALUES clause
    uris_str = " ".join(f"<{uri}>" for uri in article_uris)
    query = f"""
    PREFIX schema: <http://schema.org/>
    PREFIX ex: <http://example.org/>
    PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>

    SELECT ?article ?title ?abstract ?datePublished ?access ?meshTermLabel
    WHERE {{
      VALUES ?article {{ {uris_str} }}

      ?article a ex:Article ;
               schema:name ?title ;
               schema:description ?abstract ;
               schema:datePublished ?datePublished ;
               ex:access ?access ;
               schema:about ?meshTerm .
      ?meshTerm rdfs:label ?meshTermLabel .

      FILTER (?access IN ({", ".join(map(str, access_values))}))
    }}
    """

    # Execute the query
    results = graph.query(query)

    # Extract the details for each article
    results_dict = {}
    for row in results:
        article_uri = str(row['article'])
        if article_uri not in results_dict:
            results_dict[article_uri] = {
                'title': str(row['title']),
                'abstract': str(row['abstract']),
                'date_published': str(row['datePublished']),
                'access': str(row['access']),
                'mesh_terms': []
            }
        results_dict[article_uri]['mesh_terms'].append(str(row['meshTermLabel']))

    return results_dict

access_values = [3,5,7]
filtered_articles = filter_articles_by_access(g, ranked_article_uris, access_values)

# Output the results
for uri, details in filtered_articles.items():
    print(f"Article URI: {uri}")
    print(f"Title: {details['title']}")
    print(f"Abstract: {details['abstract']}")
    print(f"Date Published: {details['date_published']}")
    print(f"Access: {details['access']}")
    print()
```

有一篇文章用户无法访问。剩余的四篇文章是：

+   **第2篇文章：** *“小唾液腺起源的肌上皮瘤。光学和电子显微镜研究。”* 标签：牙龈肿瘤、唾液腺肿瘤。访问级别：5

+   **第4篇文章：** *“针对烟草用户的口腔恶性病变筛查可行性研究。”* 标签：口腔肿瘤。访问级别：7

+   **第1篇文章：** *“****牙龈转移作为上皮样恶性间皮瘤多脏器扩散的首个征兆。”* 标签：牙龈肿瘤。访问级别：3

+   **文章 14：** *“腮腺淋巴结转移：CT和MRI影像学发现。”* 这篇文章讨论的是腮腺肿瘤，腮腺是主要的唾液腺。标签：腮腺肿瘤。访问级别：3

## 带有向量化知识图谱的RAG

最后，让我们看看当我们将向量数据库与知识图谱结合时，RAG是如何工作的。提醒一下，您可以直接对向量数据库运行RAG，并将其发送给LLM以获取生成的响应：

```py
response = (
    client.query
    .get("Articles_with_abstracts_and_URIs", ["title", "abstractText",'article_URI','meshMajor'])
    .with_near_text({"concepts": ["therapies for mouth neoplasms"]})
    .with_limit(3)
    .with_generate(grouped_task="Summarize the key information here in bullet points. Make it understandable to someone without a medical degree.")
    .do()
)

print(response["data"]["Get"]["Articles_with_abstracts_and_URIs"][0]["_additional"]["generate"]["groupedResult"])
```

在这个例子中，我使用了搜索词“口腔肿瘤的治疗方法”，并使用相同的提示“总结出关键的要点，以项目符号的形式呈现。使其对没有医学学位的人也能理解。”我们仅返回排名前三的文章来生成此响应。以下是结果：

```py
- Metastatic malignant mesothelioma to the oral cavity is rare, with an average survival rate of 9-12 months.
- Neoadjuvant chemotherapy and radical pleurectomy decortication followed by radiotherapy were used in 13 patients from August 2012 to September 2013.
- In January 2014, 11 patients were still alive with a median survival of 11 months, while 8 patients had a recurrence and 2 patients died at 8 and 9 months after surgery.
- A 68-year-old man had a gingival mass that turned out to be a metastatic deposit of malignant mesothelioma, leading to multiorgan recurrence.
- Biopsy is important for new growing lesions, even in uncommon sites, when there is a history of mesothelioma.

- Neoadjuvant radiochemotherapy for locally advanced rectal carcinoma can be effective, but some patients may not respond well.
- Genetic alterations may be associated with sensitivity or resistance to neoadjuvant therapy in rectal cancer.
- Losses of chromosomes 1p, 8p, 17p, and 18q, and gains of 1q and 13q were found in rectal cancer tumors.
- Alterations in specific chromosomal regions were associated with the response to neoadjuvant therapy.
- The cytogenetic profile of tumor cells may influence the response to radiochemotherapy in rectal cancer.

- Intensity-modulated radiation therapy for nasopharyngeal carcinoma achieved good long-term outcomes in terms of local control and overall survival.
- Acute toxicities included mucositis, dermatitis, and xerostomia, with most patients experiencing Grade 0-2 toxicities.
- Late toxicity mainly included xerostomia, which improved over time.
- Distant metastasis remained the main cause of treatment failure, highlighting the need for more effective systemic therapy.
```

作为测试，我们可以确切看到选择了哪三篇文章：

```py
# Extract article URIs
article_uris = [article["article_URI"] for article in response["data"]["Get"]["Articles_with_abstracts_and_URIs"]]

# Function to filter the response for only the given URIs
def filter_articles_by_uri(response, article_uris):
    filtered_articles = []

    articles = response['data']['Get']['Articles_with_abstracts_and_URIs']
    for article in articles:
        if article['article_URI'] in article_uris:
            filtered_articles.append(article)

    return filtered_articles

# Filter the response
filtered_articles = filter_articles_by_uri(response, article_uris)

# Output the filtered articles
print("Filtered articles:")
for article in filtered_articles:
    print(f"Title: {article['title']}")
    print(f"URI: {article['article_URI']}")
    print(f"Abstract: {article['abstractText']}")
    print(f"MeshMajor: {article['meshMajor']}")
    print("---")
```

有趣的是，第一篇文章讨论的是牙龈肿瘤，这是口腔肿瘤的一个子集，但第二篇文章是关于直肠癌的，第三篇是关于鼻咽癌的。它们是关于癌症治疗的，只不过不是我搜索的那种癌症。令人担忧的是，提示是“口腔肿瘤的治疗方法”，而结果却包含了其他类型癌症的治疗信息。这有时被称为‘上下文污染’——无关或误导性的信息被注入到提示中，导致LLM给出误导性的回应。

我们可以使用KG来解决上下文污染问题。以下是向量数据库和KG如何协同工作以实现更好的RAG实现的示意图：

![](../Images/0d64f10c44a2ffa30daa4a207c094e12.png)

作者提供的图片

首先，我们使用相同的提示对向量数据库进行语义搜索：口腔癌的治疗方法。这次我将限制增加到了20篇文章，因为我们将会过滤掉一些。

```py
response = (
    client.query
    .get("articles_with_abstracts_and_URIs", ["title", "abstractText", "meshMajor", "article_URI"])
    .with_additional(["id"])
    .with_near_text({"concepts": ["therapies for mouth neoplasms"]})
    .with_limit(20)
    .do()
)

# Extract article URIs
article_uris = [article["article_URI"] for article in response["data"]["Get"]["Articles_with_abstracts_and_URIs"]]

# Print the extracted article URIs
print("Extracted article URIs:")
for uri in article_uris:
    print(uri)
```

接下来，我们使用与之前相同的排序技术，利用与口腔肿瘤相关的概念：

```py
from rdflib import URIRef

# Constructing the SPARQL query with a FILTER for the article URIs
query = """
PREFIX schema: <http://schema.org/>
PREFIX ex: <http://example.org/>

SELECT ?article ?title ?abstract ?datePublished ?access ?meshTerm
WHERE {
  ?article a ex:Article ;
           schema:name ?title ;
           schema:description ?abstract ;
           schema:datePublished ?datePublished ;
           ex:access ?access ;
           schema:about ?meshTerm .

  ?meshTerm a ex:MeSHTerm .

  # Filter to include only articles from the list of URIs
  FILTER (?article IN (%s))
}
"""

# Convert the list of URIRefs into a string suitable for SPARQL
article_uris_string = ", ".join([f"<{str(uri)}>" for uri in article_uris])

# Insert the article URIs into the query
query = query % article_uris_string

# Dictionary to store articles and their associated MeSH terms
article_data = {}

# Run the query for each MeSH term
for mesh_term in mesh_terms:
    results = g.query(query, initBindings={'meshTerm': mesh_term})

    # Process results
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

        # Add the MeSH term to the set for this article
        article_data[article_uri]['meshTerms'].add(str(row['meshTerm']))

# Rank articles by the number of matching MeSH terms
ranked_articles = sorted(
    article_data.items(),
    key=lambda item: len(item[1]['meshTerms']),
    reverse=True
)

# Output results
for article_uri, data in ranked_articles:
    print(f"Title: {data['title']}")
    print(f"Abstract: {data['abstract']}")
    print("MeSH Terms:")
    for mesh_term in data['meshTerms']:
        print(f"  - {mesh_term}")
    print()
```

只有三篇文章被标记为与口腔肿瘤相关的术语：

+   **文章 4：** *“针对烟草使用者进行口腔恶性病变筛查的可行性研究。”* 标签：口腔肿瘤。

+   **文章 15：** *“光敏剂介导的光动力治疗化学诱导的口腔粘膜前癌病变及口腔鳞状细胞癌。”* 这篇文章讨论的是一种针对口腔癌的实验性癌症治疗方法（光动力治疗），并在大鼠身上进行了测试。标签：口腔腺瘤。

+   **文章 1：** *“牙龈转移作为上皮样恶性间皮瘤多脏器扩散的首个标志。”* 标签：牙龈肿瘤。

让我们把这些发送给LLM看看结果是否有所改善：

```py
# Filter the response
filtered_articles = filter_articles_by_uri(response, matching_articles)

# Function to combine titles and abstracts into one chunk of text
def combine_abstracts(filtered_articles):
    combined_text = "\n\n".join(
        [f"Title: {article['title']}\nAbstract: {article['abstractText']}" for article in filtered_articles]
    )
    return combined_text

# Combine abstracts from the filtered articles
combined_text = combine_abstracts(filtered_articles)

# Generate and print the summary
summary = generate_summary(combined_text)
print(summary)
```

以下是结果：

```py
- Oral cavity cancer is common and often not detected until it is advanced
- A feasibility study was conducted to improve early detection of oral cancer and premalignant lesions in a high-risk region
- Tobacco vendors were involved in distributing flyers to smokers for free examinations by general practitioners
- 93 patients were included in the study, with 27% being referred to a specialist
- 63.6% of referred patients actually saw a specialist, with 15.3% being diagnosed with a premalignant lesion
- Photodynamic therapy (PDT) was studied as an experimental cancer therapy in rats with chemically-induced premalignant lesions and squamous cell carcinoma of the palatal mucosa
- PDT was performed using Photofrin and two different activation wavelengths, with better results seen in the 514.5 nm group
- Gingival metastasis from malignant mesothelioma is extremely rare, with a low survival rate
- A case study showed a patient with a gingival mass as the first sign of multiorgan recurrence of malignant mesothelioma, highlighting the importance of biopsy for all new lesions, even in uncommon anatomical sites.
```

我们确实看到了一些改进——这些结果与直肠癌或鼻咽肿瘤无关。这看起来像是对所选三篇文章的一个相对准确的总结，三篇文章都涉及口腔肿瘤的治疗方法。

# 结论

总的来说，向量数据库在快速启动搜索、相似性（推荐）和RAG应用程序方面表现优秀。几乎不需要额外的开销。如果你的结构化数据中关联了非结构化数据，比如在这个期刊文章的例子中，它也能很好地工作。例如，如果我们没有将文章摘要作为数据集的一部分，这种方法就不太奏效了。

知识图谱（KG）在准确性和控制方面非常有效。如果你想确保进入搜索应用程序的数据是“正确的”，而这里的“正确”是指根据你的需求所决定的内容，那么就需要使用知识图谱。知识图谱对于搜索和相似性工作效果良好，但它们能满足需求的程度将取决于元数据的丰富性以及标签的质量。标签质量的定义也可能因使用场景不同而有所不同——例如，如果你构建的是推荐引擎而非搜索引擎，构建和应用分类法的方式可能会有所不同。

使用知识图谱过滤向量数据库的结果可以得到最佳效果。这并不令人惊讶——我使用知识图谱来过滤掉**由我自己决定的**无关或误导性结果，因此根据我的标准，结果自然会更好。但重点在于：并不是说知识图谱本身一定能改善结果，而是知识图谱为你提供了控制输出的能力，从而优化结果。
