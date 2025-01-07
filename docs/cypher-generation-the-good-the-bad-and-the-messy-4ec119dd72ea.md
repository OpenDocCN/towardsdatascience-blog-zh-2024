# Cypher 生成：好、坏与混乱

> 原文：[https://towardsdatascience.com/cypher-generation-the-good-the-bad-and-the-messy-4ec119dd72ea?source=collection_archive---------6-----------------------#2024-01-29](https://towardsdatascience.com/cypher-generation-the-good-the-bad-and-the-messy-4ec119dd72ea?source=collection_archive---------6-----------------------#2024-01-29)

## *创建用于文本到 Cypher 生成的微调数据集的方法。*

[](https://medium.com/@silviaonofrei?source=post_page---byline--4ec119dd72ea--------------------------------)[![Silvia Onofrei](../Images/198b04b2063b4269eaff52402dc5f8d5.png)](https://medium.com/@silviaonofrei?source=post_page---byline--4ec119dd72ea--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4ec119dd72ea--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4ec119dd72ea--------------------------------) [Silvia Onofrei](https://medium.com/@silviaonofrei?source=post_page---byline--4ec119dd72ea--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4ec119dd72ea--------------------------------) ·13分钟阅读·2024年1月29日

--

![](../Images/ad8b418ba27894f9c93e3debc77b318b.png)

由 ChatGPT-DALLE 创建

# 引言

Cypher 是 Neo4j 的图查询语言。它的灵感来源于 SQL，并且与 SQL 有许多相似之处，能够从知识图谱中检索数据。随着生成式 AI 的兴起以及大型语言模型（LLMs）的广泛应用，自然而然地会有人问，哪些 LLM 能生成 Cypher 查询，或者我们如何微调自己的模型从文本生成 Cypher 查询。

这个问题提出了相当大的挑战，主要是由于微调数据集的稀缺，并且在我看来，这样的数据集将极大地依赖于特定的图谱模式。

在这篇博客文章中，我将讨论几种创建微调数据集的方法，旨在从文本中生成 Cypher 查询。第一种方法基于大型语言模型（LLMs），并使用预定义的图谱模式。第二种方法完全基于 Python，提供了一种灵活的方式，可以生成各种各样的问题和 Cypher 查询，适用于任何图谱模式。在实验中，我创建了一个基于 ArXiv 数据集子集的知识图谱。

当我完成这篇博客时，Tomaz Bratanic 启动了一个 [倡议项目](https://medium.com/@bratanic-tomaz/crowdsourcing-text2cypher-dataset-e65ba51916d4)，旨在开发一个全面的微调数据集，涵盖各种图谱模式，并结合人工智能参与的方法来生成和验证 Cypher 语句。我希望这里讨论的见解也能对该项目有所帮助。

# 知识图谱模型

我喜欢使用ArXiv科学文章数据集，因为它具有干净且易于集成到知识图谱中的格式。利用我最近的[Medium博客文章](https://medium.com/towards-data-science/leverage-keybert-hdbscan-and-zephyr-7b-beta-to-build-a-knowledge-graph-33d7534ee01b)中的技术，我通过添加关键词和聚类增强了这个数据集。由于我的主要关注点是构建一个微调数据集，我将省略构建该图谱的具体细节。对于感兴趣的读者，详细信息可以在这个[Github仓库](https://github.com/SolanaO/Blogs_Content/tree/master/cypher_generator)中找到。

该图谱的规模合理，包含超过38K个节点和近96K个关系，拥有9种节点标签和8种关系类型。其架构如下图所示：

![](../Images/9d2e0017b08013546e0efeae020a78c9.png)

图片来源：作者

虽然这个知识图谱尚未完全优化，还可以进一步改进，但它在这篇博客文章中能够有效地实现目的。如果你更倾向于直接测试查询而不构建图谱，我已将数据转储文件上传至这个[Github仓库](https://github.com/SolanaO/Blogs_Content/tree/master/cypher_generator)。

# 使用LLM生成训练对

我实现的第一种方法灵感来自Tomaz Bratanic的博客文章，关于[构建知识图谱聊天机器人](https://medium.com/neo4j/knowledge-graph-based-chatbot-with-gpt-3-and-neo4j-c4ebbd325ed)和[使用H2O Studio微调LLM模型](/fine-tuning-an-llm-model-with-h2o-llm-studio-to-generate-cypher-statements-3f34822ad5)。最初，在提示中提供了一些示例查询。然而，最近的一些模型增强了直接从图谱架构生成Cypher查询的能力。因此，除了GPT-4或GPT-4-turbo之外，现在还有可访问的开源替代方案，如Mixtral-8x7B，我预期它能够有效生成高质量的训练数据。

在这个项目中，我尝试了两种模型。为了方便起见，我决定将GPT-4-turbo与ChatGPT结合使用，详情请见这个[Colab笔记本](https://github.com/SolanaO/Blogs_Content/blob/master/cypher_generator/4_ArXiv_KG_Synthetic_Data_OpenAI.ipynb)。然而，在这个[笔记本](https://github.com/SolanaO/Blogs_Content/blob/master/cypher_generator/5_ArXiv_KG_Synthetic_Data_Mixtral.ipynb)中，我对Mixtral-7x2B-GPTQ模型进行了一些测试，它是一个足够小的量化模型，可以在Google Colab上运行，并且能够提供令人满意的结果。

为了保持数据的多样性并有效监控生成的提问和Cypher语句对，我采用了一个两步法：

+   第1步：提供完整的架构给LLM，并请求其生成10–15种不同类别的与图谱相关的潜在问题及其描述。

+   第2步：提供架构信息并指示LLM为每个识别的类别创建特定数量N的训练对。

## 提取样本类别：

在这一步，我使用了 ChatGPT Pro 版本，尽管我确实多次调整提示，结合并增强了输出结果。

+   将图的模式提取为字符串（有关更多信息，请参见下一部分）。

+   构建一个提示来生成类别：

```py
chatgpt_categories_prompt = f"""
You are an experienced and useful Python and Neo4j/Cypher developer.

I have a knowledge graph for which I would like to generate 
interesting questions which span 12 categories (or types) about the graph. 
They should cover single nodes questions,
two or three more nodes, relationships and paths. Please suggest 12
categories together with their short descriptions. 
Here is the graph schema:
{schema}
 """
```

+   请 LLM 生成类别。

+   审查、修正并根据需要增强类别。以下是一个示例：

```py
'''Authorship and Collaboration: Questions about co-authorship and collaboration patterns.
For example, "Which authors have co-authored articles the most?"''',
'''Article-Author Connections: Questions about the relationships between articles and authors,
such as finding articles written by a specific author or authors of a particular article.
For example, "Find all the authors of the article with tile 'Explorations of manifolds'"''',
'''Pathfinding and Connectivity: Questions that involve paths between multiple nodes,
such as tracing the relationship path from an article to a topic through keywords, 
or from an author to a journal through their articles.
For example, "How is the author 'John Doe' connected to the journal 'Nature'?"'''
```

💡**提示**💡

+   *如果图的模式非常大，拆分为重叠的子图（这也取决于图的拓扑结构），并对每个子图重复上述过程。*

+   *在使用开源模型时，选择最适合你计算资源的模型。* [*TheBloke*](https://huggingface.co/TheBloke) *发布了一个详尽的量化模型清单，* [*Neo4j GenAI*](https://neo4j.com/emil/introducing-genai-stack-developers/) *提供了在你自己的硬件上工作的工具，* [*LightningAI Studio*](https://lightning.ai/onboarding) *是一个最近发布的平台，提供对多种 LLMs 的访问。*

## 生成训练对：

这一步是通过 OpenAI API 执行的，使用 GPT-4-turbo，它也有输出 JSON 格式的选项。同样，图的模式通过提示提供：

```py
def create_prompt(schema, category):
    """Build and format the prompt."""
    formatted_prompt = [
        {"role": "system",
        "content": "You are an experienced Cypher developer and a 
helpful assistant designed to output JSON!"},
        {"role": "user",
         "content": f"""Generate 40 questions and their corresponding 
Cypher statements about the Neo4j graph database with 
the following schema:
        {schema}
        The questions should cover {category} and should be phrased 
in a natural conversational manner. Make the questions diverse 
and interesting.
        Make sure to use the latest Cypher version and that all
 the queries are working Cypher queries for the provided graph. 
You may add values for the node attributes as needed. 
Do not add any comments, do not label or number the questions.
        """}]
    return formatted_prompt
```

构建一个函数来提示模型并检索输出：

```py
def prompt_model(messages):
    """Function to produce and extract model's generation."""
    response = client.chat.completions.create(
        model="gpt-4-1106-preview", # work with gpt-4-turbo
        response_format={"type": "json_object"},
        messages=messages)
    return response.choices[0].message.content
```

遍历类别并将输出收集到一个列表中：

```py
def build_synthetic_data(schema, categories):
    """Function to loop through the categories and generate data."""

    # List to collect all outputs
    full_output=[]
    for category in categories:
        # Prompt the model and retrieve the generated answer
        output = [prompt_model(create_prompt(schema, category))]
        # Store all the outputs in a list
        full_output += output
    return full_output

# Generate 40 pairs for each of the categories
full_output = build_synthetic_data(schema, categories)

# Save the outputs to a file
write_json(full_output, data_path + synthetic_data_file)
```

在项目的这一阶段，我收集了近 500 对问题和 Cypher 语句。以下是一个示例：

```py
{"Question": "What articles have been written by 'John Doe'?",
"Cypher": "MATCH (a:Author {first_name:'John', last_name:'Doe'})-
[:WRITTEN_BY]-(article:Article) RETURN article.title, article.article_id;"}
```

数据需要大量清理和整理。虽然并不复杂，但过程既耗时又繁琐。以下是我遇到的一些挑战：

+   由于 Cypher 语句不完整，存在非 JSON 条目；

+   期望的格式是 {’question’: ‘某个问题’, ‘cypher’:’某个 cypher’}，但偏差很常见，需要进行标准化；

+   在问题和 Cypher 语句聚集在一起的情况下，需要将它们分开并进行组织。

💡**提示**💡

*与其从一开始就试图找到最好的提示格式，不如多次迭代提示的变体。根据我的经验，即使经过仔细调整，像这样的大量数据生成不可避免地会出现一些偏差。*

现在说说内容方面。GPT-4-turbo 很有能力生成关于图的好问题，但并非所有的 Cypher 语句都是有效的（工作 Cypher）和正确的（提取预期信息）。在生产环境中进行微调时，我会纠正或删除这些错误的语句。

我创建了一个函数 `execute_cypher_queries()`，它将查询发送到 Neo4j 图数据库。如果出现错误，它会记录一条消息，或者从数据库中检索输出。此函数可以在此 [Google Colab 笔记本](https://github.com/SolanaO/Blogs_Content/blob/master/cypher_generator/4_ArXiv_KG_Synthetic_Data_OpenAI.ipynb) 中找到。

从提示中，你可能会注意到我指示 LLM 生成模拟数据以填充属性值。虽然这种方法更简单，但它导致图中的许多空输出。而且，这需要额外的努力来识别涉及幻觉的语句，如虚构的属性：

```py
'MATCH (author:Author)-[:WRITTEN_BY]-(article:Article)-[:UPDATED]-
(updateDate:UpdateDate) 
WHERE article.creation_date = updateDate.update_date 
RETURN DISTINCT author.first_name, author.last_name;"
```

`Article` 节点在 ArXiv 图中没有 `creation_date` 属性！

💡**提示**💡

*为了减少空输出，我们可以直接从图中提取实例。这些实例可以被纳入提示中，并指示 LLM 使用这些信息来丰富 Cypher 语句。*

# 构建功能查询

这种方法可以生成从数百到数十万条正确的 Cypher 查询，具体取决于图的大小和复杂性。然而，至关重要的是要在查询的数量和多样性之间找到平衡。尽管这些查询在任何图中都是正确且适用的，但它们有时会显得公式化或僵化。

## 提取有关图结构的信息

对于这个过程，我们需要从一些数据提取和准备开始。我使用了 Cypher 查询和来自 [neo4j_graph.py](https://github.com/langchain-ai/langchain/blob/master/libs/community/langchain_community/graphs/neo4j_graph.py) 模块的一些代码。

+   连接到现有的 Neo4j 图形数据库。

+   提取 JSON 格式的模式。

+   从图中提取几个节点和关系实例，即从图中提取数据作为样本，用于填充查询。

我创建了一个执行这些步骤的 Python 类，它可以在 [Github 仓库](https://github.com/SolanaO/Blogs_Content/tree/master/cypher_generator)中的 `utils/neo4j_schema.py` 找到。有了这些，只需几行代码即可提取图的相关数据：

```py
# Initialize the Neo4j connector
graph = Neo4jGraph(url=URI, username=USER, password=PWD)
# Initialize the schema extractor module
gutils = Neo4jSchema(url=URI, username=USER, password=PWD)

# Build the schema as a JSON object
jschema = gutils.get_structured_schema
# Retrieve the list of nodes in the graph
nodes = get_nodes_list(jschema)
# Read the nodes with their properties and their datatypes
node_props_types = jschema['node_props']

# Check the output
print(f"The properties of the node Report are:\n{node_props_types['Report']}")

>>>The properties of the node Report are:
   [{'property': 'report_id', 'datatype': 'STRING'}, {'property': 'report_no', 'datatype': 'STRING'}]

# Extract a list of relationships
relationships = jschema['relationships']

# Check the output
relationships[:1]

>>>[{'start': 'Article', 'type': 'HAS_KEY', 'end': 'Keyword'},
 {'start': 'Article', 'type': 'HAS_DOI', 'end': 'DOI'}]
```

## 从图中提取数据

这些数据将为我们填充 Cypher 查询提供真实的值。

+   首先，我们提取几个节点实例，这将检索图中所有节点的数据，包括标签、属性及其值：

```py
# Extract node samples from the graph - 4 sets of node samples
node_instances = gutils.extract_node_instances(
                  nodes, # list of nodes to extract labels
                  4)  # how many instances to extract for each node
```

+   接下来，提取关系实例，包括起始节点、关系的类型和属性以及终止节点信息：

```py
# Extract relationship instances
rels_instances = gutils.extract_multiple_relationships_instances(
                relationships, # list of relationships to extract instances for
                8)  # how many instances to extract for each relationship
```

💡**提示**💡

+   *上述两种方法适用于完整的节点列表、关系或它们的子列表。*

+   *如果图中包含缺少某些属性记录的实例，建议收集更多实例，以确保覆盖所有可能的场景。*

下一步是序列化数据，通过将 Neo4j.time 的值替换为字符串，并将其保存到文件中。

## 解析提取的数据

我把这个阶段称为 Python 体操。在这里，我们处理前一步获得的数据，包括图模式、节点实例和关系实例。我们重新格式化这些数据，以使其更易于访问我们正在开发的函数。

+   我们首先使用以下方法识别图中的所有数据类型：

```py
dtypes = retrieve_datatypes(jschema)
dtypes

>>>{'DATE', 'INTEGER', 'STRING'}
```

+   对于每种数据类型，我们提取具有该数据类型的属性（及相应的节点）。

+   我们解析每种数据类型的实例。

+   我们还会处理和筛选关系，使得起始节点和结束节点具有特定数据类型的属性。

所有代码都可以在[Github 仓库](https://github.com/SolanaO/Blogs_Content/tree/master/cypher_generator)中找到。这样做的原因将在下一节中透明化。

## 如何构建一个或一千个 Cypher 语句

作为一个数学家，我通常通过潜在的函数来理解陈述。让我们考虑以下示例：

```py
q = "Find the Topic whose description contains 'Jordan normal form'!"
cq = "MATCH (n:Topic) WHERE n.description CONTAINS 'Jordan normal form' RETURN n"
```

上述内容可以视为多个变量`f(x, y, z)`和`g(x, y, z)`的函数，其中

```py
f(x, y, z) = f"Find the {x} whose {y} contains {z}!"
q = f('Topic', 'description', 'Jordan normal form')

g(x, y, z) = f"MATCH (n:{x}) WHERE n.{y} CONTAINS {z} RETURN n"
qc = g('Topic', 'description', 'Jordan normal form')
```

我们能构建多少这种类型的查询？为了简化论证，假设有`N`个节点标签，每个标签平均有`n`个属性，这些属性的数据类型为`STRING`。因此，我们至少可以构建`Nxn`个查询，不考虑字符串选择`z`的选项。

💡**提示**💡

*仅仅因为我们能够通过一行代码构建所有这些查询，并不意味着我们应该将整个查询示例集纳入我们的微调数据集中。*

## 制定一个流程和模板

主要挑战在于创建一个足够多样化的查询列表，覆盖与图谱相关的广泛方面。由于专有和开源的大型语言模型（LLM）能够生成基本的 Cypher 语法，我们的重点可以转向生成有关图谱中节点和关系的查询，而忽略特定语法的查询。为了收集查询示例并将其转换为功能形式，可以参考任何 Cypher 语言书籍或访问[Neo4j Cypher 文档网站](https://neo4j.com/docs/cypher-manual/current/introduction/)。

在[GitHub 仓库](https://github.com/SolanaO/Blogs_Content/tree/master/cypher_generator)中，有大约60种此类查询，它们被应用于 ArXiv 知识图谱。这些查询具有多功能性，适用于任何图谱模式。

以下是创建一组相似查询并将其纳入微调数据集的完整 Python 函数：

```py
def find_nodes_connected_to_node_via_relation():
    def prompter(label_1, prop_1, rel_1, label_2):
        subschema = get_subgraph_schema(jschema, [label_1, label_2], 2, True)
        message = {"Prompt": "Convert the following question into a Cypher query using the provided graph schema!",
                   "Question": f"""For each {label_1}, find the number of {label_2} linked via {rel_1} and retrieve the {prop_1} of the {label_1} and the {label_2} counts in ascending order!""",
                   "Schema": f"Graph schema: {subschema}",
                   "Cypher": f"MATCH (n:{label_1}) -[:{rel_1}]->(m:{label_2}) WITH DISTINCT n, m RETURN n.{prop_1} AS {prop_1}, count(m) AS {label_2.lower()}_count ORDER BY {label_2.lower()}_count"
        }
        return message

    sampler=[]
    for e in all_rels:
        for k, v in e[1].items():
            temp_dict = prompter(e[0], k, e[2], e[3])
            sampler.append(temp_dict)

    return sampler
```

+   函数find_nodes_connected_to_node_via_relation()接受生成的提示并对all_rels中的所有元素进行评估，all_rels是提取和处理后的关系实例集合，条目的形式为：

```py
['Keyword',
 {'name': 'logarithms', 'key_id': '720452e14ca2e4e07b76fa5a9bc0b5f6'},
 'HAS_TOPIC',
 'Topic',
 {'cluster': 0}]
```

+   提示输入为两个节点，分别表示为`label_1`和`label_2`，属性`prop_1`针对`label_1`，关系`rel_1`，

+   `message`包含了相应条目在微调数据集中的提示组件，

+   `subschema`提取表示`label_1`和`label_2`的两个节点的第一邻居，这意味着：列出的两个节点，它们所有相关的节点（图中的距离为1），以及关系和所有对应的属性。

💡**提示**💡

*将* `*subschema*` *包括在微调数据集中并非必要，尽管提示与微调数据的对齐程度越高，生成的输出往往越好。从我的角度来看，将 subschema 纳入微调数据仍然具有优势。*

# 结论

总结来说，本文探索了多种构建微调数据集以从文本生成 Cypher 查询的方法。以下是这些技术的概述，包括它们的优缺点：

**LLM 生成的问题和 Cypher 语句对**：

+   这种方法在数据收集方面看似直接，但它通常需要过多的数据清理。

+   虽然某些专有的 LLM 产生了较好的结果，但许多开源的 LLM 仍然缺乏生成多种准确 Cypher 语句的能力。

+   当图模式复杂时，这种技术会变得繁琐。

**功能性方法或参数查询生成：**

+   这种方法可以适应不同的图模式，并允许轻松扩展样本大小。然而，重要的是确保数据不会变得过于重复，并保持多样性。

+   它需要大量的 Python 编程。生成的查询常常显得机械，缺乏对话的语气。

要扩展这些方法之外：

+   图模式可以无缝地融入创建功能查询的框架中。考虑以下问题和 Cypher 语句对：

```py
Question: Which articles were written by the author whose last name is Doe?
Cypher: "MATCH (a:Article) -[:WRITTEN_BY]-> (:Author {last_name: 'Doe') RETURN a"
```

我们可以通过基本的解析（例如将 WRITTEN_BY 替换为 written by），而不是直接使用参数化，来增强生成问题的自然性。

这突显了图形模式设计和图形实体标注在微调解析构建中的重要性。遵循一般规范，比如使用名词作为节点标签，使用富有提示性的动词作为关系，证明是有益的，并且能够在元素之间创建更自然的对话式联系。

+   最后，至关重要的是不要忽视从图形交互中收集实际用户生成查询的价值。若有可能，通过参数化这些查询或通过其他方法增强它们会非常有用。最终，这种方法的有效性取决于图形设计的具体目标。

为此，重要的是要提到，我的重点是在较简单的 Cypher 查询上。我没有涉及创建或修改图中的数据，也没有涉及图模式，且没有包括 APOC 查询。

*你是否能提出其他方法或想法，用于生成这样的微调问题和 Cypher 语句对？*

# 资源

**代码**

[Github 仓库：Knowledge_Graphs_Assortment](https://www.notion.so/GraphRAG-Unleashing-the-Power-of-KG-with-LLM-51cf4430ea3349ff9320b3375fe47fe5?pvs=21) — 用于构建 ArXiv 知识图谱

[Github 仓库：Cypher_Generator](https://github.com/SolanaO/Blogs_Content/tree/master/cypher_generator) — 包含与本文相关的所有代码

**数据**

• 学术文章仓库：[arXiv 数据集](https://www.kaggle.com/datasets/Cornell-University/arxiv)，该数据集拥有[CC0: 公共领域](https://creativecommons.org/publicdomain/zero/1.0/)许可证。
