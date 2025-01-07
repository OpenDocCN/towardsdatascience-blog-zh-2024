# 使用 LangGraph 构建幻想足球研究代理

> 原文：[`towardsdatascience.com/building-a-fantasy-football-research-agent-with-langgraph-ad8deb0126f1?source=collection_archive---------7-----------------------#2024-12-05`](https://towardsdatascience.com/building-a-fantasy-football-research-agent-with-langgraph-ad8deb0126f1?source=collection_archive---------7-----------------------#2024-12-05)

## 一份全面的指南，涵盖了与 Sleeper API 的集成、Streamlit 用户界面的创建以及通过 AWS CDK 的部署。

[](https://evandiewald.medium.com/?source=post_page---byline--ad8deb0126f1--------------------------------)![Evan Diewald](https://evandiewald.medium.com/?source=post_page---byline--ad8deb0126f1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ad8deb0126f1--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ad8deb0126f1--------------------------------) [Evan Diewald](https://evandiewald.medium.com/?source=post_page---byline--ad8deb0126f1--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ad8deb0126f1--------------------------------) ·阅读时长 9 分钟·2024 年 12 月 5 日

--

![](img/6164ac579dfc394bf298bcb623544676.png)

照片由 [Dmitriy Demidov](https://unsplash.com/@fotograw?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我花费如此多时间思考我的幻想足球队，真是令人尴尬。

管理一个队伍意味着要处理海量的信息——伤病报告、专家预测、即将到来的休赛周和有利的对阵情况。而且这不仅仅是数据量的问题，更是数据的短暂性——如果你的明星跑卫在周三的训练中拉伤了大腿筋，你最好不要根据周二的报告做出阵容决策。

这就是为什么像 Anthropic 的 Claude 和 OpenAI 的 ChatGPT 这样的通用聊天机器人在幻想足球推荐中基本上无用的原因，因为它们仅限于一个静态的训练语料库，该语料库早在几个月甚至几年前就已经停止更新。

例如，如果我们询问 Claude Sonnet 3.5 谁是当前最好的跑卫，我们会看到像 Christian McCaffrey、Breece Hall 和 Travis Etienne 这样的名字，这些球员在 2024 年的赛季中都受到伤病困扰或表现令人失望。此时没有提到 Saquon Barkley 或 Derrick Henry——这两位明显的领跑者。（不过值得一提的是，Claude 会披露其局限性。）

![](img/6700132629c292799231a2c0c0f1580b.png)

像 [Perplexity](https://www.perplexity.ai/) 这样的应用程序更为准确，因为它们确实访问了一个拥有最新信息的搜索引擎。然而，它当然不了解我的整个阵容情况、我们联盟的季后赛局势或我们守护规则的细微差别。

有机会为每个用户定制一个以幻想足球为主题的**代理**，提供工具和个性化的上下文。

让我们深入探讨实现过程。

## 架构概览

聊天机器人的核心将是一个基于[ReAct](https://arxiv.org/abs/2210.03629)框架的[LangGraph](https://www.langchain.com/langgraph)代理。我们将让它访问与[Sleeper API](https://docs.sleeper.com/)集成的工具，用于常见操作，如查看联盟排名、名单、球员统计、专家分析等。

除了 LangGraph API 服务器，我们的后端还将包括一个小型 Postgres 数据库和 Redis 缓存，用于管理状态和路由请求。我们将使用[Streamlit](https://streamlit.io/)来构建一个简单但有效的用户界面。

在开发过程中，我们可以通过[Docker Compose](https://docs.docker.com/compose/)在本地运行所有这些组件，但我也将展示基础设施即代码（IaC）来部署一个可扩展的堆栈，使用[AWS CDK](https://docs.aws.amazon.com/cdk/api/v2/)。

## Sleeper API 集成

[Sleeper](https://sleeper.com) 慷慨地提供了一个公开的只读 API，我们可以利用它获取用户和联盟的详细信息，包括完整的球员名单、名单和选秀信息。尽管没有明确文档化，但我还发现了一些 GraphQL 端点，提供关键的统计数据、预测以及——也许最有价值的——NFL 记者的最新专家分析。

我创建了一个简单的 API 客户端来访问各种方法，你可以在[这里](https://github.com/evandiewald/fantasy-football-agent/blob/main/fantasy_chatbot/sleeper.py)找到它。我想要强调的一个技巧是`requests-cache` [库](https://requests-cache.readthedocs.io/en/stable/index.html)。我不想成为 Sleeper 免费数据集的贪婪客户端，所以我将响应缓存到本地的 Sqlite 数据库，并使用基本的 TTL 机制。

这样不仅减少了重复的 API 流量对 Sleeper 服务器的压力（降低了他们将我的 IP 地址列入黑名单的机会），而且大大降低了我的客户端的延迟，提供更好的用户体验。

设置和使用缓存非常简单，正如你在这个代码片段中看到的那样——

```py
import requests_cache
from urllib.parse import urljoin
from typing import Union, Optional
from pathlib import Path

class SleeperClient:
    def __init__(self, cache_path: str = '../.cache'):

        # config
        self.cache_path = cache_path
        self.session = requests_cache.CachedSession(
            Path(cache_path) / 'api_cache', 
            backend='sqlite',
            expire_after=60 * 60 * 24,
        )

        ...

    def _get_json(self, path: str, base_url: Optional[str] = None) -> dict:
        url = urljoin(base_url or self.base_url, path)
        return self.session.get(url).json()

    def get_player_stats(self, player_id: Union[str, int], season: Optional[int] = None, group_by_week: bool = False):
        return self._get_json(
            f'stats/nfl/player/{player_id}?season_type=regular&season={season or self.nfl_state["season"]}{"&grouping=week" if group_by_week else ""}',
            base_url=self.stats_url,
        )
```

所以运行类似的命令

`self.session.get(url)`

首先检查本地 Sqlite 缓存中是否有未过期的响应。如果找到，我们可以跳过 API 调用，直接从数据库中读取。

## 定义工具

我想把 Sleeper API 客户端转化为一组关键功能，供代理使用以提供响应。因为这些功能将由 LLM 有效地调用，所以我认为为它们做清晰的注解并要求简单、灵活的参数是很重要的。

例如，Sleeper 的 API 通常要求提供数字化的球员 ID，这对于编程接口是有意义的。然而，我希望将这个概念从 LLM 中抽象出来，让它仅仅输入球员的名字进行这些功能。为了确保额外的灵活性，并允许像拼写错误这样的情况，我实现了一种基本的“[模糊搜索](https://github.com/rapidfuzz/RapidFuzz)”方法，将球员名字的搜索映射到他们对应的球员 ID。

```py
# file: fantasy_chatbot/league.py

def get_player_id_fuzzy_search(self, player_name: str) -> tuple[str, str]:
  # will need a simple search engine to go from player name to player id without needing exact matches. returns the player_id and matched player name as a tuple
  nearest_name = process.extract(query=player_name, choices=self.player_names, scorer=fuzz.WRatio, limit=1)[0]
  return self.player_name_to_id[nearest_name[0]], self.player_names[nearest_name[2]]

# example usage in a tool
def get_player_news(self, player_name: Annotated[str, "The player's name."]) -> str:
    """
    Get recent news about a player for the most up-to-date analysis and injury status.
    Use this whenever naming a player in a potential deal, as you should always have the right context for a recommendation.
    If sources are provided, include markdown-based link(s)
    (e.g. [Rotoballer](https://www.rotoballer.com/player-news/saquon-barkley-has-historic-night-sunday/1502955) )
    at the bottom of your response to provide proper attribution
    and allow the user to learn more.
    """
    player_id, player_name = self.get_player_id_fuzzy_search(player_name)
    # news
    news = self.client.get_player_news(player_id, limit=3)
    player_news = f"Recent News about {player_name}\n\n"
    for n in news:
        player_news += f"**{n['metadata']['title']}**\n{n['metadata']['description']}"
        if analysis := n['metadata'].get('analysis'):
            player_news += f"\n\nAnalysis:\n{analysis}"
        if url := n['metadata'].get('url'):
            # markdown link to source
            player_news += f"\n[{n['source'].capitalize()}]({url})\n\n"

   return player_news
```

这比简单的名称到球员 ID 的映射更好，因为它允许拼写错误和其他打字错误，例如`saquon` → `Saquon Barkley`

![](img/091f3735f1f8c100a869129af236093f.png)

我基于这些原则创建了一些有用的工具：

+   获取联盟状态（排名、当前周数、季后赛球队数等）

+   获取队伍拥有者的名单

+   获取球员新闻（关于球员的最新文章/分析）

+   获取球员数据（本赛季每周得分以及对阵情况）

+   获取球员当前拥有者（对于提出交易至关重要）

+   获取每个位置上的最佳可用球员（自由球员市场）

+   获取球员排名（到目前为止的表现，按位置分类）

你可能还能想到一些有用的功能可以添加，比如关于最近交易、联盟对战情况和选秀信息的详细数据。

## LangGraph 代理

整个项目的推动力来源于一个学习 LangGraph 生态系统的机会，这可能正在成为构建智能工作流的事实标准。

我过去曾从零开始构建代理，如果当时我知道 LangGraph 就好了。它不仅仅是一个薄的封装层，围绕着各种 LLM 提供者，它为构建、部署和监控复杂工作流提供了巨大的实用性。如果你有兴趣深入了解，可以查看 LangChain Academy 的[LangGraph 简介](https://academy.langchain.com/courses/intro-to-langgraph)课程。

如前所述，图形本身基于 ReAct 框架，这是一种流行且有效的方法，可以使大语言模型（LLM）与外部工具进行交互，例如上述定义的工具。

我还添加了一个节点，用于持久化每个用户的长期记忆，以便信息可以跨会话保存。我希望我们的代理能够“记住”用户的关注点、偏好和之前推荐的交易，因为这是我在见过的聊天机器人中并没有特别好实现的功能。以图形的形式，它看起来是这样的：

![](img/436f6bbcdcdf128744ef4cb4a3d1ecaa.png)

很简单吧？再说一次，你可以查看完整的图定义在[代码](https://github.com/evandiewald/fantasy-football-agent/blob/main/fantasy_chatbot/chatbot.py)中，但我会重点介绍`write_memory`节点，它负责为每个用户写入和更新个人资料。这使我们能够在有效利用令牌的同时，跟踪关键的交互。

```py
def write_memory(state: MessagesState, config: RunnableConfig, store: BaseStore):
    """Reflect on the chat history and save a memory to the store."""

    # get the username from the config
    username = config["configurable"]["username"]

    # retrieve existing memory if available
    namespace = ("memory", username)
    existing_memory = store.get(namespace, "user_memory")

    # format the memories for the instruction
    if existing_memory and existing_memory.value:
        memory_dict = existing_memory.value
        formatted_memory = (
            f"Team Name: {memory_dict.get('team_name', 'Unknown')}\n"
            f"Current Concerns: {memory_dict.get('current_concerns', 'Unknown')}"
            f"Other Details: {memory_dict.get('other_details', 'Unknown')}"
        )
    else:
        formatted_memory = None

    system_msg = CREATE_MEMORY_INSTRUCTION.format(memory=formatted_memory)

    # invoke the model to produce structured output that matches the schema
    new_memory = llm_with_structure.invoke([SystemMessage(content=system_msg)] + state['messages'])

    # overwrite the existing user profile
    key = "user_memory"
    store.put(namespace, key, new_memory)
```

这些记忆被展示在[系统提示](https://github.com/evandiewald/fantasy-football-agent/blob/main/fantasy_chatbot/prompts.py)中，我在其中还向 LLM 提供了有关我们联盟的基本信息，以及我希望它如何处理常见的用户请求。

## Streamlit 用户界面和演示

我不是前端开发人员，因此用户界面 heavily 依赖于 Streamlit 的组件和常见的聊天机器人模式。用户输入他们的 Sleeper 用户名，用于查找可用的联赛并在不同的线程间持久化记忆。

我还添加了一些附加功能，比如实现了令牌流式传输，以便用户能够从 LLM 中获得即时反馈。另一个重要部分是“研究面板”，它展示了代理工具调用的结果，用户可以检查每个回应背后的原始数据。

这是一个快速演示。

## 部署

对于开发，我建议通过提供的 `docker-compose.yml` 文件将组件本地部署。这将在 `http://localhost:8123` 上本地暴露 API，因此您可以快速测试更改并从本地 Streamlit 应用程序连接。

![](img/5afeba0c67645f244863f81be5d72e78.png)

我还包含了一个基于 AWS CDK 的 IaC，用于将应用程序托管到互联网。大多数资源定义见[此处](https://github.com/evandiewald/fantasy-football-agent/blob/main/deploy/lib/fantasy-football-agent-stack.ts)。请注意 `docker-compose.yml` 和与 ECS 设置相关的 CDK 代码之间的相似之处：

`docker-compose.yml` 中的 LangGraph API 容器片段：

```py
# from docker-compose.yml

langgraph-api:
    image: "fantasy-chatbot"
    ports:
        - "8123:8000"
    healthcheck:
        test: curl --request GET --url http://localhost:8000/ok
        timeout: 1s
        retries: 5
        interval: 5s
    depends_on:
        langgraph-redis:
            condition: service_healthy
        langgraph-postgres:
            condition: service_healthy
    env_file: "../.env"
    environment:
        REDIS_URI: redis://langgraph-redis:6379
        POSTGRES_URI: postgres://postgres:postgres@langgraph-postgres:5432/postgres?sslmode=disable// file: fantasy-football-agent-stack.ts
```

这里是 CDK 堆栈中类似的设置：

```py
// fantasy-football-agent-stack.ts

const apiImageAsset = new DockerImageAsset(this, 'apiImageAsset', {
  directory: path.join(__dirname, '../../fantasy_chatbot'),
  file: 'api.Dockerfile',
  platform: assets.Platform.LINUX_AMD64,
});
const apiContainer = taskDefinition.addContainer('langgraph-api', {
  containerName: 'langgraph-api',
  image: ecs.ContainerImage.fromDockerImageAsset(apiImageAsset),
  portMappings: [{
    containerPort: 8000,
  }],
  environment: {
    ...dotenvMap,
    REDIS_URI: 'redis://127.0.0.1:6379',
    POSTGRES_URI: 'postgres://postgres:postgres@127.0.0.1:5432/postgres?sslmode=disable'
  },
  logging: ecs.LogDrivers.awsLogs({
    streamPrefix: 'langgraph-api',
  }),
});

apiContainer.addContainerDependencies(
  {
    container: redisContainer,
    condition: ecs.ContainerDependencyCondition.HEALTHY,
  },
  {
    container: postgresContainer,
    condition: ecs.ContainerDependencyCondition.HEALTHY,
  },
)
```

除了一些微妙的差异，它实际上是 1:1 的翻译，这也是我在比较本地环境与“生产”部署时总是关注的地方。`DockerImageAsset` 是一个特别有用的资源，因为它在合成期间处理构建和部署（到 ECR）Docker 镜像的工作。

> **注意：**通过`npm run cdk deploy`将堆栈部署到您的 AWS 账户将会产生费用。在这个演示代码中，我没有为 Streamlit 应用程序添加任何密码保护，这意味着任何拥有 URL 的人都可以使用聊天机器人！如果您计划自己部署，强烈建议添加一些额外的安全措施。

## 重点

**你需要保持工具的简洁性**。这个应用做了很多事情，但仍然缺少一些关键功能，如果我只是添加更多工具，它会开始崩溃。未来，我希望将图形拆分成任务特定的子组件，例如“新闻分析师”代理和“统计学家”代理。

**可追溯性和调试在基于代理的应用程序中比传统软件更为重要**。尽管模型在生成结构化输出方面取得了显著进展，但基于 LLM 的函数调用仍然本质上不如传统程序可靠。我在调试过程中广泛使用了 LangSmith。

**在语言模型商品化的时代，可靠的记者无可替代。** 我们正处于这样一个阶段，任何人都能在一个周末内制作出一个合理的聊天机器人，那么产品如何区分自己并建立护城河呢？没有分析师和专家提供的高质量报道，这个应用程序（或任何类似的应用程序）将毫无用处。换句话说，伊恩·拉帕波特（Ian Rapaport）和马修·贝里（Matthew Berry）这样的专家比以往任何时候都更加珍贵。

## Repo

[](https://github.com/evandiewald/fantasy-football-agent/tree/main?source=post_page-----ad8deb0126f1--------------------------------) [## GitHub - evandiewald/fantasy-football-agent

### 在 GitHub 上创建帐户，为 evandiewald/fantasy-football-agent 开发做出贡献。

[github.com](https://github.com/evandiewald/fantasy-football-agent/tree/main?source=post_page-----ad8deb0126f1--------------------------------)

*所有图片，除非另有说明，均为作者提供。*
