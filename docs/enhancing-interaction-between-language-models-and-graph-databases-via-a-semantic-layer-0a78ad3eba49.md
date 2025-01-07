# 通过语义层增强语言模型与图数据库之间的交互

> 原文：[https://towardsdatascience.com/enhancing-interaction-between-language-models-and-graph-databases-via-a-semantic-layer-0a78ad3eba49?source=collection_archive---------0-----------------------#2024-01-18](https://towardsdatascience.com/enhancing-interaction-between-language-models-and-graph-databases-via-a-semantic-layer-0a78ad3eba49?source=collection_archive---------0-----------------------#2024-01-18)

## 为LLM代理提供一套强大的工具，以便其与图数据库进行交互

[](https://bratanic-tomaz.medium.com/?source=post_page---byline--0a78ad3eba49--------------------------------)[![Tomaz Bratanic](../Images/d5821aa70918fcb3fc1ff0013497b3d5.png)](https://bratanic-tomaz.medium.com/?source=post_page---byline--0a78ad3eba49--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0a78ad3eba49--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0a78ad3eba49--------------------------------) [Tomaz Bratanic](https://bratanic-tomaz.medium.com/?source=post_page---byline--0a78ad3eba49--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0a78ad3eba49--------------------------------) ·阅读时长11分钟·2024年1月18日

--

知识图谱通过灵活的数据模式提供了对数据的出色表示，能够存储[结构化和非结构化信息](https://medium.com/neo4j/using-a-knowledge-graph-to-implement-a-devops-rag-application-b6ba24831b16)。你可以使用Cypher语句从图数据库（如Neo4j）中检索信息。一种选择是使用大型语言模型（LLMs）生成Cypher语句。虽然这种方式提供了极大的灵活性，但事实是，基础LLMs在始终如一地生成精确的Cypher语句方面仍然不够稳定。因此，我们需要寻找一种替代方案，以确保一致性和稳健性。那么，如果LLM从用户输入中提取参数，而不是直接开发Cypher语句，并根据用户意图使用预定义的函数或Cypher模板呢？简而言之，你可以为LLM提供一套预定义的工具和使用指令，指示何时以及如何基于用户输入使用它们，这也被称为语义层。

![](../Images/aeeb64a2a96d1a41bf353ad6817a5d4e.png)

语义层是一个中间步骤，提供了一种更精确、更稳健的方式，使LLMs与知识图谱进行交互。图片来自作者。灵感来源于[这张图片](https://cube.dev/blog/semantic-layer-the-backbone-of-ai-powered-data-experiences)。

语义层由多个暴露给大语言模型的工具组成，它可以用来与知识图谱进行互动。这些工具的复杂性各异。你可以将语义层中的每个工具看作一个函数。例如，看看以下这个函数。

```py
def get_information(entity: str, type: str) -> str:
    candidates = get_candidates(entity, type)
    if not candidates:
        return "No information was found about the movie or person in the database"
    elif len(candidates) > 1:
        newline = "\n"
        return (
            "Need additional information, which of these "
            f"did you mean: {newline + newline.join(str(d) for d in candidates)}"
        )
    data = graph.query(
        description_query, params={"candidate": candidates[0]["candidate"]}
    )
    return data[0]["context"]
```

这些工具可以有多个输入参数，正如上面的例子所示，这使你能够实现复杂的工具。此外，工作流不仅仅可以由数据库查询组成，还允许你处理任何边缘情况或异常，具体取决于你的需求。其优势在于，你将可能大部分时间有效的提示工程问题，转化为每次都能精确执行的代码工程问题。

## 电影代理

在这篇博客文章中，我们将展示如何实现一个语义层，使大语言模型代理能够与包含演员、电影及其评分信息的知识图谱进行互动。

![](../Images/e04d930fdbbd3e70efc04a3060abfcff.png)

电影代理架构。图像由作者提供。

取自文档（同样由我编写）：

> 代理利用多种工具有效地与Neo4j图数据库进行交互。
> 
> *** 信息工具**：检索有关电影或个人的数据，确保代理能够访问最新和最相关的信息。
> 
> *** 推荐工具**：根据用户的偏好和输入提供电影推荐。
> 
> *** 内存工具**：在知识图谱中存储有关用户偏好的信息，从而在多次互动中提供个性化体验。

代理可以使用信息或推荐工具从数据库中检索信息，或使用内存工具将用户偏好存储到数据库中。

预定义的函数和工具使得代理能够编排复杂的用户体验，引导用户朝着特定目标前进，或提供与其当前用户旅程位置相关的定制信息。

这种预定义方法通过减少大语言模型的艺术自由度，从而增强了系统的稳健性，确保回应更加结构化，并与预定的用户流程对齐，从而改善了整体的用户体验。

电影代理的语义层后端已经实现，并作为一个[LangChain模板](https://github.com/langchain-ai/langchain/tree/master/templates/neo4j-semantic-layer)可用。我使用了这个模板构建了一个简单的Streamlit聊天应用程序。

![](../Images/8e94a84617ebaa1796729ef1cae7a77d.png)

Streamlit聊天界面。图像由作者提供。

代码可在[GitHub](https://github.com/tomasonjo/llm-movieagent)上获取。你可以通过定义环境变量并执行以下命令来启动项目：

```py
docker-compose up
```

## 图模型

该图基于[MovieLens](https://grouplens.org/datasets/movielens/)数据集。它包含有关演员、电影和10万条电影评分的信息。

![](../Images/1498878706b68d43ef3619c7bbbec6a1.png)

图谱架构。图像由作者提供。

该可视化图展示了一个知识图谱，包含那些参与过电影表演或导演的个人，并且按类型进行了进一步分类。每个电影节点包含其发布日期、标题和 IMDb 评分信息。图谱中还包含用户评分，我们可以利用这些评分提供推荐。

你可以通过执行位于文件夹根目录的 `ingest.py` 脚本来填充图谱。

## 定义工具

现在，我们将定义代理可以用来与知识图谱互动的工具。我们从**信息工具**开始。信息工具的设计目的是获取有关演员、导演和电影的相关信息。Python 代码如下所示：

```py
def get_information(entity: str, type: str) -> str:
    # Use full text index to find relevant movies or people
    candidates = get_candidates(entity, type)
    if not candidates:
        return "No information was found about the movie or person in the database"
    elif len(candidates) > 1:
        newline = "\n"
        return (
            "Need additional information, which of these "
            f"did you mean: {newline + newline.join(str(d) for d in candidates)}"
        )
    data = graph.query(
        description_query, params={"candidate": candidates[0]["candidate"]}
    )
    return data[0]["context"]
```

函数首先通过全文索引查找提到的相关人物或电影。[Neo4j 中的全文索引](https://neo4j.com/docs/cypher-manual/current/indexes-for-full-text-search/)底层使用 Lucene。它支持无缝实现基于文本距离的查找，允许用户拼写错误的词语也能获取到结果。如果没有找到相关实体，我们可以直接返回响应。另一方面，如果识别出多个候选项，我们可以引导代理向用户询问后续问题，并让他们对感兴趣的电影或人物给出更具体的信息。假设用户问：“约翰是谁？”。

```py
print(get_information("John", "person"))
# Need additional information, which of these did you mean: 
# {'candidate': 'John Lodge', 'label': 'Person'}
# {'candidate': 'John Warren', 'label': 'Person'}
# {'candidate': 'John Gray', 'label': 'Person'}
```

在这种情况下，工具通知代理需要额外的信息。通过简单的提示工程，我们可以引导代理询问用户后续问题。假设用户提供了足够具体的信息，这样工具就能识别特定的电影或人物。在这种情况下，我们使用带参数的 Cypher 语句来检索相关信息。

```py
print(get_information("Keanu Reeves", "person"))
# type:Actor
# title: Keanu Reeves
# year: 
# ACTED_IN: Matrix Reloaded, The, Side by Side, Matrix Revolutions, The, Sweet November, Replacements, The, Hardball, Matrix, The, Constantine, Bill & Ted's Bogus Journey, Street Kings, Lake House, The, Chain Reaction, Walk in the Clouds, A, Little Buddha, Bill & Ted's Excellent Adventure, The Devil's Advocate, Johnny Mnemonic, Speed, Feeling Minnesota, The Neon Demon, 47 Ronin, Henry's Crime, Day the Earth Stood Still, The, John Wick, River's Edge, Man of Tai Chi, Dracula (Bram Stoker's Dracula), Point Break, My Own Private Idaho, Scanner Darkly, A, Something's Gotta Give, Watcher, The, Gift, The
# DIRECTED: Man of Tai Chi
```

有了这些信息，代理可以回答大多数关于基努·里维斯的问题。

现在，让我们引导代理有效地使用这个工具。幸运的是，借助 LangChain，这一过程既简单又高效。首先，我们使用 Pydantic 对象定义函数的输入参数。

```py
class InformationInput(BaseModel):
    entity: str = Field(description="movie or a person mentioned in the question")
    entity_type: str = Field(
        description="type of the entity. Available options are 'movie' or 'person'"
    )
```

在这里，我们说明了 entity 和 entity_type 参数都是字符串类型。entity 参数输入被定义为问题中提到的电影或人物。而对于 entity_type，我们也提供了可选项。在处理低基数（即有少量不同值）的情况时，我们可以直接向 LLM 提供可选项，让它使用有效的输入。正如我们之前看到的，我们使用全文索引来消除电影或人物的歧义，因为有太多值不能直接在提示中提供。

现在，让我们将这一切汇总到信息工具定义中。

```py
class InformationTool(BaseTool):
    name = "Information"
    description = (
        "useful for when you need to answer questions about various actors or movies"
    )
    args_schema: Type[BaseModel] = InformationInput

    def _run(
        self,
        entity: str,
        entity_type: str,
        run_manager: Optional[CallbackManagerForToolRun] = None,
    ) -> str:
        """Use the tool."""
        return get_information(entity, entity_type)
```

准确简洁的工具定义是语义层的重要组成部分，以便代理在需要时能够正确选择相关工具。

推荐工具稍微复杂一些。

```py
def recommend_movie(movie: Optional[str] = None, genre: Optional[str] = None) -> str:
    """
    Recommends movies based on user's history and preference
    for a specific movie and/or genre.
    Returns:
        str: A string containing a list of recommended movies, or an error message.
    """
    user_id = get_user_id()
    params = {"user_id": user_id, "genre": genre}
    if not movie and not genre:
        # Try to recommend a movie based on the information in the db
        response = graph.query(recommendation_query_db_history, params)
        try:
            return ", ".join([el["movie"] for el in response])
        except Exception:
            return "Can you tell us about some of the movies you liked?"
    if not movie and genre:
        # Recommend top voted movies in the genre the user haven't seen before
        response = graph.query(recommendation_query_genre, params)
        try:
            return ", ".join([el["movie"] for el in response])
        except Exception:
            return "Something went wrong"

    candidates = get_candidates(movie, "movie")
    if not candidates:
        return "The movie you mentioned wasn't found in the database"
    params["movieTitles"] = [el["candidate"] for el in candidates]
    query = recommendation_query_movie(bool(genre))
    response = graph.query(query, params)
    try:
        return ", ".join([el["movie"] for el in response])
    except Exception:
        return "Something went wrong"
```

首先需要注意的是，这两个输入参数都是可选的。因此，我们需要引入处理所有可能输入参数组合和缺失情况的工作流。为了生成个性化推荐，我们首先获取`user_id`，然后将其传递到下游的Cypher推荐语句中。

与之前一样，我们需要向代理呈现函数的输入。

```py
class RecommenderInput(BaseModel):
    movie: Optional[str] = Field(description="movie used for recommendation")
    genre: Optional[str] = Field(
        description=(
            "genre used for recommendation. Available options are:" f"{all_genres}"
        )
    )
```

由于只有20种可用的电影类型，我们将它们的值作为提示的一部分提供。为了避免电影歧义，我们再次在函数内部使用全文索引。像之前一样，我们以工具定义结束，以通知LLM何时使用它。

```py
class RecommenderTool(BaseTool):
    name = "Recommender"
    description = "useful for when you need to recommend a movie"
    args_schema: Type[BaseModel] = RecommenderInput

    def _run(
        self,
        movie: Optional[str] = None,
        genre: Optional[str] = None,
        run_manager: Optional[CallbackManagerForToolRun] = None,
    ) -> str:
        """Use the tool."""
        return recommend_movie(movie, genre)
```

到目前为止，我们已经定义了两个工具来从数据库中检索数据。然而，信息流不必是单向的。例如，当用户告诉代理他们已经看过某部电影并且可能喜欢它时，我们可以将该信息存储在数据库中，并在未来的推荐中使用它。此时，记忆工具将非常有用。

```py
def store_movie_rating(movie: str, rating: int):
    user_id = get_user_id()
    candidates = get_candidates(movie, "movie")
    if not candidates:
        return "This movie is not in our database"
    response = graph.query(
        store_rating_query,
        params={"user_id": user_id, "candidates": candidates, "rating": rating},
    )
    try:
        return response[0]["response"]
    except Exception as e:
        print(e)
        return "Something went wrong"

class MemoryInput(BaseModel):
    movie: str = Field(description="movie the user liked")
    rating: int = Field(
        description=(
            "Rating from 1 to 5, where one represents heavy dislike "
            "and 5 represent the user loved the movie"
        )
    )
```

记忆工具有两个强制性的输入参数，用于定义电影及其评分。这是一个简单的工具。我需要提到的一点是，在我的测试中，我注意到可能需要提供何时给出特定评分的示例，因为LLM开箱即用时并不是最擅长这方面的处理。

## 代理

现在让我们通过使用[LangChain表达式语言](https://python.langchain.com/docs/expression_language/)（LCEL）来定义一个代理，把所有内容整合在一起。

```py
llm = ChatOpenAI(temperature=0, model="gpt-4", streaming=True)
tools = [InformationTool(), RecommenderTool(), MemoryTool()]

llm_with_tools = llm.bind(functions=[format_tool_to_openai_function(t) for t in tools])

prompt = ChatPromptTemplate.from_messages(
    [
        (
            "system",
            "You are a helpful assistant that finds information about movies "
            " and recommends them. If tools require follow up questions, "
            "make sure to ask the user for clarification. Make sure to include any "
            "available options that need to be clarified in the follow up questions "
            "Do only the things the user specifically requested. ",
        ),
        MessagesPlaceholder(variable_name="chat_history"),
        ("user", "{input}"),
        MessagesPlaceholder(variable_name="agent_scratchpad"),
    ]
)

agent = (
    {
        "input": lambda x: x["input"],
        "chat_history": lambda x: _format_chat_history(x["chat_history"])
        if x.get("chat_history")
        else [],
        "agent_scratchpad": lambda x: format_to_openai_function_messages(
            x["intermediate_steps"]
        ),
    }
    | prompt
    | llm_with_tools
    | OpenAIFunctionsAgentOutputParser()
)

agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True).with_types(
    input_type=AgentInput, output_type=Output
)
```

LangChain表达式语言使得定义代理并暴露其所有功能变得非常方便。我们不会深入探讨LCEL语法，因为那超出了本博客的范围。

电影代理后台作为API端点通过[LangServe](https://www.langchain.com/langserve)暴露。

## Streamlit聊天应用

现在我们只需要实现一个Streamlit应用，它连接到LangServe API端点，就可以开始使用了。我们将看看用于检索代理响应的异步函数。

```py
async def get_agent_response(
    input: str, stream_handler: StreamHandler, chat_history: Optional[List[Tuple]] = []
):
    url = "http://api:8080/movie-agent/"
    st.session_state["generated"].append("")
    remote_runnable = RemoteRunnable(url)
    async for chunk in remote_runnable.astream_log(
        {"input": input, "chat_history": chat_history}
    ):
        log_entry = chunk.ops[0]
        value = log_entry.get("value")
        if isinstance(value, dict) and isinstance(value.get("steps"), list):
            for step in value.get("steps"):
                stream_handler.new_status(step["action"].log.strip("\n"))
        elif isinstance(value, str):
            st.session_state["generated"][-1] += value
            stream_handler.new_token(value)
```

函数`get_agent_response`旨在与电影代理API进行交互。它向API发送包含用户输入和聊天历史的请求，然后异步处理API的响应。该函数处理不同类型的响应，更新流处理器的新状态，并将生成的文本附加到会话状态中，从而使我们能够将结果流式传输给用户。

现在让我们进行测试。

![](../Images/bce1fd1386d8ea222ccafa7738aa6b16.png)

电影代理在运行中。图像由作者提供。

结果显示，电影代理提供了与用户出乎意料的良好和引导式互动。

## 结论

总结来说，在语言模型与图数据库交互中引入语义层，如我们在电影代理（Movie Agent）中所示，代表了提升用户体验和数据交互效率的一次重大飞跃。通过将重点从生成任意的Cypher语句转移到利用结构化、预定义的工具和功能套件，语义层为语言模型的交互带来了一个新的精确性和一致性水平。这种方法不仅简化了从知识图谱中提取相关信息的过程，还确保了一个更具目标导向和用户中心的体验。

语义层充当着桥梁的角色，将用户意图转换为具体、可执行的查询，语言模型能够准确可靠地执行这些查询。因此，用户受益于一个不仅能够更有效理解他们查询的系统，而且还能更轻松、减少歧义地引导他们朝着预期的结果前进。此外，通过将语言模型的响应限制在这些预定义工具的范围内，我们降低了错误或无关输出的风险，从而增强了系统的可信度和可靠性。

代码可在[GitHub](https://github.com/tomasonjo/llm-movieagent)上找到。

## 数据集

F. Maxwell Harper 和 Joseph A. Konstan. 2015. 《MovieLens数据集：历史与背景》。ACM交互智能系统交易（TiiS）5, 4: 19:1–19:19. [https://doi.org/10.1145/2827872](https://doi.org/10.1145/2827872)
