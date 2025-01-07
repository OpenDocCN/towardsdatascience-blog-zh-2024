# 在Cypher语句生成中评估LLMs

> 原文：[https://towardsdatascience.com/evaluating-llms-in-cypher-statement-generation-c570884089b3?source=collection_archive---------2-----------------------#2024-01-19](https://towardsdatascience.com/evaluating-llms-in-cypher-statement-generation-c570884089b3?source=collection_archive---------2-----------------------#2024-01-19)

## 评估生成的Cypher语句准确性的分步教程

[](https://bratanic-tomaz.medium.com/?source=post_page---byline--c570884089b3--------------------------------)[![Tomaz Bratanic](../Images/d5821aa70918fcb3fc1ff0013497b3d5.png)](https://bratanic-tomaz.medium.com/?source=post_page---byline--c570884089b3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c570884089b3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c570884089b3--------------------------------) [Tomaz Bratanic](https://bratanic-tomaz.medium.com/?source=post_page---byline--c570884089b3--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c570884089b3--------------------------------) ·阅读时间9分钟·2024年1月19日

--

![](../Images/88372b2e2c33a680583b4fc6098d1e1a.png)

由DALL-E生成的图像

大型语言模型（LLMs）开始流行后不久，我们意识到它们在将自然语言转换为SQL和Cypher等数据库查询方面相当不错。为了使LLM能够为您的特定数据库生成定制的查询，您必须提供其架构，并可选地提供一些示例查询。有了这些信息，LLM可以根据自然语言输入生成数据库查询。

尽管LLMs在将自然语言转换为数据库查询方面展示了巨大的潜力，但它们仍然远非完美。因此，理解它们的表现如何，通过评估过程来评估其性能是至关重要的。幸运的是，生成SQL语句的过程已经在学术界得到了研究，例如[Spider](https://arxiv.org/abs/1809.08887)等研究。我们将使用以下指标来评估LLMs的Cypher生成能力。

+   **Jaro-Winkler：** 这是一种基于编辑距离的文本相似度度量。我们将生成的Cypher查询与正确的Cypher查询进行比较，并通过编辑查询所需的差异，衡量两个字符串之间的不同程度。

+   **Pass@1：** 如果生成的查询从数据库中返回的结果与正确的Cypher语句相同，则得分为1.0，否则为0.0。

+   **Pass@3：** 与Pass@1类似，不同之处在于我们生成3个查询，而不是1个查询。如果其中任何一个查询的结果与正确的查询相同，则得分为1.0，否则为0.0。

+   **Jaccard相似度：**它衡量由生成的Cypher语句返回的响应与正确的Cypher响应之间的Jaccard相似度。这个度量标准旨在捕捉模型可能返回几乎正确的结果的示例。

代码可以在[GitHub](https://github.com/tomasonjo/blogs/blob/master/llm/evaluating_cypher.ipynb)上找到，这是与[Adam Schill Collberg](https://medium.com/u/8b125c49d121?source=post_page---user_mention--c570884089b3--------------------------------)合作开发的。

正如你所观察到的，重点是评估来自数据库的响应，而不是实际的Cypher语句本身。一个原因是，Cypher语句可以有多种写法来获取相同的信息。我们不关心LLM偏好哪种语法；我们只关心它是否能生成正确的响应。此外，我们对于LLM在响应中如何命名列没有强烈偏好，因此我们不希望评估它的列命名能力等……

## 测试数据集

测试数据集由问题和相关的Cypher语句对组成。

你可以使用LLM来生成测试数据集的建议。然而，你需要手动验证这些示例，因为LLM可能会出错，并且并不是100%可靠。如果它们完全可靠，我们就不需要测试它们了。由于我们是基于数据库结果而不是Cypher语句本身来进行评估的，因此我们需要一个运行中的数据库，并且必须包含我们可以使用的相关信息。在这篇博客文章中，我们将使用[Neo4j Sandbox中的推荐项目](http://ndbox.neo4j.com/?usecase=recommendations)。该推荐项目使用了[MovieLens数据集](https://grouplens.org/datasets/movielens/)，该数据集包含了电影、演员、评分等信息。推荐项目也可以在演示服务器上以只读访问的形式使用，这意味着如果你不想创建新的数据库实例，完全不需要这么做。

![](../Images/62d91638c35854e38d31dbd85606726e.png)

推荐项目图谱模式。图像由作者提供。

在这个例子中，我使用了GPT-4来为训练数据集生成建议，然后逐一检查并在需要的地方进行修正。我们将仅使用27个测试对。在实际应用中，你可能需要使用至少几百个示例。

```py
data = [
    {
        "question": "How many movies were released in 1995?",
        "cypher": "MATCH (m:Movie) WHERE m.Year = 1995 RETURN count(*) AS result",
    },
    {
        "question": "Who directed the movie Inception?",
        "cypher": "MATCH (m:Movie {title: 'Inception'})<-[:DIRECTED]-(d) RETURN d.name",
    },
    {
        "question": "Which actors played in the movie Casino?",
        "cypher": "MATCH (m:Movie {title: 'Casino'})<-[:ACTED_IN]-(a) RETURN a.name",
    },
    {
        "question": "How many movies has Tom Hanks acted in?",
        "cypher": "MATCH (a:Actor {name: 'Tom Hanks'})-[:ACTED_IN]->(m:Movie) RETURN count(m)",
    },
    {
        "question": "List all the genres of the movie Schindler's List",
        "cypher": "MATCH (m:Movie {title: 'Schindler\\'s List'})-[:IN_GENRE]->(g:Genre) RETURN g.name",
    },
    ...
]
```

## 生成Cypher语句

我们将使用[LangChain](https://www.langchain.com/)来生成Cypher语句。[Neo4jGraph](https://python.langchain.com/docs/use_cases/graph/graph_cypher_qa)对象在LangChain中建立与Neo4j的连接，并检索其模式信息。

```py
graph = Neo4jGraph()
print(graph.schema)
# Node properties are the following:
# Movie {posterEmbedding: LIST, url: STRING, runtime: INTEGER, revenue: INTEGER, budget: INTEGER, plotEmbedding: LIST, imdbRating: FLOAT, released: STRING, countries: LIST, languages: LIST, plot: STRING, imdbVotes: INTEGER, imdbId: STRING, year: INTEGER, poster: STRING, movieId: STRING, tmdbId: STRING, title: STRING}
# Genre {name: STRING}
# User {userId: STRING, name: STRING}
# Actor {url: STRING, bornIn: STRING, bio: STRING, died: DATE, born: DATE, imdbId: STRING, name: STRING, poster: STRING, tmdbId: STRING}
# Director {url: STRING, bornIn: STRING, born: DATE, died: DATE, tmdbId: STRING, imdbId: STRING, name: STRING, poster: STRING, bio: STRING}
# Person {url: STRING, bornIn: STRING, bio: STRING, died: DATE, born: DATE, imdbId: STRING, name: STRING, poster: STRING, tmdbId: STRING}
# Relationship properties are the following:
# RATED {rating: FLOAT, timestamp: INTEGER}
# ACTED_IN {role: STRING}
# DIRECTED {role: STRING}
# The relationships are the following:
# (:Movie)-[:IN_GENRE]->(:Genre)
# (:User)-[:RATED]->(:Movie)
# (:Actor)-[:ACTED_IN]->(:Movie)
# (:Actor)-[:DIRECTED]->(:Movie)
# (:Director)-[:DIRECTED]->(:Movie)
# (:Director)-[:ACTED_IN]->(:Movie)
# (:Person)-[:ACTED_IN]->(:Movie)
# (:Person)-[:DIRECTED]->(:Movie)
```

架构包含节点标签、它们的属性以及相应的关系。接下来，我们将使用LangChain表达式语言定义一个提示，发送给LLM，指示它将自然语言翻译为Cypher语句，以检索相关信息来回答问题。欲了解有关LangChain表达式语言的更多详情，请访问[官方文档](https://python.langchain.com/docs/expression_language/)。

```py
cypher_template = """Based on the Neo4j graph schema below, 
write a Cypher query that would answer the user's question.
Return only Cypher statement, no backticks, nothing else.
{schema}

Question: {question}
Cypher query:"""  # noqa: E501

cypher_prompt = ChatPromptTemplate.from_messages(
    [
        (
            "system",
            "Given an input question, convert it to a Cypher query. No pre-amble.",
        ),
        ("human", cypher_template),
    ]
)

cypher_chain = (
    RunnablePassthrough.assign(
        schema=lambda _: graph.get_schema,
    )
    | cypher_prompt
    | llm.bind(stop=["\nCypherResult:"])
    | StrOutputParser()
)
```

如果你熟悉对话型LLM，你可以识别出**系统**和**用户**消息定义。正如你所观察到的，我们将图形架构和用户问题都放入了用户消息中。生成Cypher语句的具体提示工程指令仍未解决，这意味着这里可能还有改进空间。通过评估过程，你可以看到哪种方法对特定的LLM效果最佳。在这个例子中，我们使用的是**gpt-4-turbo**。

我们可以通过以下示例来测试Cypher生成：

```py
response = cypher_chain.invoke(
    {
        "question": "How many movies have the keyword 'love' in the title and a runtime under 2 hours?"
    }
)
print(response)
# MATCH (m:Movie) 
# WHERE m.title CONTAINS 'love' AND m.runtime < 120 
# RETURN count(m) as NumberOfMovies
```

我们可以观察到，gpt-4-turbo在将自然语言翻译为Cypher语句方面表现得相当不错。现在让我们定义评估过程。

```py
# Iterate over each row with tqdm to show a progress bar
for index, row in tqdm(df.iterrows(), total=df.shape[0]):
    # Fetch data based on the test Cypher statement
    true_data = graph.query(row["cypher"])
    # Generate 3 Cypher statement and fetch data
    example_generated_cyphers = []
    example_eval_datas = []
    for _ in range(3):
        cypher = cypher_chain.invoke({"question": row["question"]})
        example_generated_cyphers.append(cypher)
        # Fetch data based on the generated Cypher statement
        try:
            example_eval_datas.append(graph.query(cypher))
        except ValueError:  # Handle syntax error
            example_eval_datas.append([{"id": "Cypher syntax error"}])

    # These metrics require only the first cypher/response
    jaro_winkler = get_jw_distance(row["cypher"], example_generated_cyphers[0])
    pass_1 = (
        1
        if df_sim_pair(
            (row["cypher"], true_data),
            (example_generated_cyphers[0], example_eval_datas[0]),
        )
        == 1
        else 0
    )
    jaccard = df_sim_pair(
        (row["cypher"], true_data),
        (example_generated_cyphers[0], example_eval_datas[0]),
    )
    # Pass@3 check all 3 responses
    pass_3 = 1 if any(
        df_sim_pair((row["cypher"], true_data), (gen_cypher, eval_data)) == 1
        for gen_cypher, eval_data in zip(example_generated_cyphers, example_eval_datas)
    ) else 0

    # Append the results to their respective lists
    generated_cyphers.append(example_generated_cyphers)
    true_datas.append(true_data)
    eval_datas.append(example_eval_datas)
    jaro_winklers.append(jaro_winkler)
    pass_1s.append(pass_1)
    pass_3s.append(pass_3)
    jaccards.append(jaccard)
```

*运行此代码大约花费了5分钟，因为我们需要生成81个响应来计算pass@3度量。*

代码稍长，但其核心概念相当简单。我们遍历存储测试示例的数据框中的所有行。接下来，我们为每个训练示例生成三个Cypher查询，并从数据库中检索相应的数据。接下来就是计算相关的度量标准并将其存储在列表中，以便我们可以评估并可视化它们。我们没有在博客文章中包括辅助函数，因为我认为逐个审查每个度量标准的代码实现没有必要。然而，所有这些函数都包含在[笔记本](https://github.com/tomasonjo/blogs/blob/master/llm/evaluating_cypher.ipynb)中。

现在让我们评估结果。

![](../Images/8aaa45fbcae5ebb94cecbd739f9b8a76.png)

LLM生成的Cypher语句评估。图片由作者提供。

评估基于四种不同的度量标准：

1.  **Jaro-Winkler**：该度量显示了0.89的高平均值，表明LLM生成的Cypher查询在字符串层面上与正确的Cypher查询非常相似。

1.  **Pass@1**：此处的平均得分为0.48，表明当每个查询独立评估时，几乎有一半生成的Cypher查询返回与正确查询完全相同的结果。

1.  **Pass@3**：该度量的平均值为0.63，表明相较于Pass@1有所提高。这意味着虽然LLM在第一次尝试时可能不会生成正确的查询，但通常会在三次尝试内生成一个正确版本。

1.  **Jaccard相似度**：平均得分为0.53，是所有指标中最低的，但仍然表明大多数情况下，LLM生成的Cypher查询的结果集与正确查询的结果集共享超过一半的元素。

总体而言，这些指标表明LLM在生成与正确查询相似的Cypher查询方面表现不错，并且通常能够生成功能等效的结果，尤其是在多次尝试后。然而，仍然有改进的空间，特别是在第一次尝试时生成正确查询的能力方面。此外，评估过程本身也有改进的余地。让我们来看一个例子：

```py
row = df.iloc[24]
# Print the desired information
print("Question:", row["question"], "\n")
print("True Cypher:", row["cypher"], "\n")
print("Generated Cypher", row["generated_cypher"][0], "\n")

# Question: Which directors have never had a movie with a rating below 6.0? 
# True Cypher: 
# MATCH (d:Director)-[:DIRECTED]->(m:Movie) 
# WITH d, MIN(m.imdbRating) AS lowestRating WHERE lowestRating >= 6.0 
# RETURN d.name, lowestRating 

# Generated Cypher
# MATCH (d:Director)-[:DIRECTED]->(m:Movie)
# WHERE NOT EXISTS {
#     MATCH (d)-[:DIRECTED]->(m2:Movie)
#     WHERE m2.imdbRating < 6.0
# }
# RETURN DISTINCT d.name 
```

对于问题“哪些导演从未拍过评分低于6.0的电影”，LLM在获取结果方面表现不错。它使用了与测试数据集不同的方法，但这不是问题，因为它应该得到相同的结果。然而，我们在测试数据中返回了电影的标题和评分。另一方面，LLM只返回了标题，而没有评分。我们不能责怪它，因为它只是按照指令执行的。然而，你必须知道，在这个例子中，Pass@1得分为0，而Jaccard相似度仅为0.5。因此，在构建测试数据集时，你必须非常小心，既要注意如何定义提示，也要注意相应的Cypher语句。

LLM的另一个特点是它们是非确定性的，这意味着每次运行可能会得到不同的结果。现在我们将连续运行三次评估。这项评估大约需要15分钟。

![](../Images/88a9e606be5ed7980e371f818c34ad89.png)

对LLM生成的Cypher语句的评估。图片来自作者。

条形图突出显示了LLM的非确定性特性。Jaro-Winkler分数在所有运行中始终较高，波动幅度在0.88和0.89之间，表明生成的查询在字符串相似度上保持稳定。然而，对于Pass@1，有明显的变化，第一次运行得分为0.52，后续运行得分分别为0.59和0.48。Pass@3得分变化较小，约为0.56到0.63之间，表明多次尝试能够获得更一致的正确结果。

## 结论

通过这篇博客文章，我们了解到像GPT-4这样的LLM在生成Cypher查询方面有着很有前景的能力，但该技术并非万无一失。所展示的评估框架提供了LLM性能的详细定量评估，允许你不断地进行实验，并更新提示工程及生成有效准确的Cypher语句所需的其他步骤。此外，它还展示了LLM的非确定性特性如何影响不同评估之间的表现。因此，你可以预期在生产环境中会遇到类似的非确定性行为。

代码可在[GitHub](https://github.com/tomasonjo/blogs/blob/master/llm/evaluating_cypher.ipynb)上找到。

## 数据集

F. Maxwell Harper 和 Joseph A. Konstan. 2015年. 《MovieLens 数据集：历史与背景》. 《ACM互动智能系统学报》（TiiS）5卷，4期：19:1–19:19. [https://doi.org/10.1145/2827872](https://doi.org/10.1145/2827872)
