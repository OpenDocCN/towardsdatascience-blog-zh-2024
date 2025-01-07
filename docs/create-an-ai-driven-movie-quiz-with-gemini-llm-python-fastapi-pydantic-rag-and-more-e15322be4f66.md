# 创建一个 AI 驱动的电影问答游戏，使用 Gemini LLM、Python、FastAPI、Pydantic、RAG 等

> 原文：[https://towardsdatascience.com/create-an-ai-driven-movie-quiz-with-gemini-llm-python-fastapi-pydantic-rag-and-more-e15322be4f66?source=collection_archive---------0-----------------------#2024-04-18](https://towardsdatascience.com/create-an-ai-driven-movie-quiz-with-gemini-llm-python-fastapi-pydantic-rag-and-more-e15322be4f66?source=collection_archive---------0-----------------------#2024-04-18)

## 了解如何通过 VertexAI 使用 Gemini 与 Python，使用 FastAPI 创建 API，使用 Pydantic 进行数据验证，以及检索增强生成（RAG）的基本原理

[](https://vojay.medium.com/?source=post_page---byline--e15322be4f66--------------------------------)[![Volker Janz](../Images/0825160d6d521f4152948f0187cf354b.png)](https://vojay.medium.com/?source=post_page---byline--e15322be4f66--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e15322be4f66--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e15322be4f66--------------------------------) [Volker Janz](https://vojay.medium.com/?source=post_page---byline--e15322be4f66--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e15322be4f66--------------------------------) ·21分钟阅读·2024年4月18日

--

![](../Images/50e962ece900e09fb6fcb997adafede4.png)

图片由 [Kenny Eliason](https://unsplash.com/@neonbrand?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在本文中，我将分享一些创建基于 LLM 的 Web 应用程序的基础知识，使用的技术包括：Python、[FastAPI](https://fastapi.tiangolo.com/)、[Pydantic](https://docs.pydantic.dev/latest/)、[VertexAI](https://cloud.google.com/vertex-ai) 等等。

> 你将学习如何从头开始创建这样一个项目，并了解其基本概念，包括**检索增强生成**（RAG）。

*免责声明：在本项目中，我使用了来自电影数据库的数据。该 API 可免费用于非商业用途，并符合《数字千年版权法案》（DMCA）。有关 TMDB 数据使用的更多信息，请* [*阅读官方 FAQ*](https://developer.themoviedb.org/docs/faq)*。*

# 目录

– [灵感来源](#bf8e)

– [系统架构](#a588)

– [理解检索增强生成（RAG）](#d0b3)

– [使用 Poetry 进行 Python 项目管理](#4cfd)

– [使用 FastAPI 创建 API](#ca13)

– [使用 Pydantic 进行数据验证和质量控制](#103f)

– [使用 httpx 的 TMDB 客户端](#fe62)

– [Gemini LLM 客户端与 VertexAI](#040c)

– [使用 Jinja 的模块化提示生成器](#2bf6)

– [前端](#f4be)

– [API 示例](#809a)

– [结论](#165c)

分享这些知识的最佳方式是通过一个实际的例子。因此，我将使用我的项目**Gemini电影侦探**来涵盖各个方面。该项目是作为[Google AI Hackathon 2024](https://googleai.devpost.com/)的一部分创建的，而我写这篇文章时，该比赛仍在进行中。

![](../Images/3d44844866ab4bb81b2e6b5acaf52bf1.png)

Gemini电影侦探（作者）

Gemini电影侦探是一个旨在利用Gemini Pro模型的强大功能通过VertexAI创建一个引人入胜的问答游戏，使用来自[电影数据库（TMDB）](https://www.themoviedb.org/)的最新电影数据。

项目的一部分还是使其可以使用Docker部署并创建一个在线版本。自己试试吧：[movie-detectives.com](https://movie-detectives.com/)。请记住，这只是一个简单的原型，所以可能会出现意外问题。此外，为了控制使用GCP和VertexAI可能产生的成本，我不得不添加一些限制。

![](../Images/f949342e71bc028936d5836525e42f0d.png)

Gemini电影侦探（作者）

该项目是**完全开源**的，分为两个独立的仓库：

+   **🚀 后端的Github仓库**: [https://github.com/vojay-dev/gemini-movie-detectives-api](https://github.com/vojay-dev/gemini-movie-detectives-api)

+   **🖥️ 前端的Github仓库**: [https://github.com/vojay-dev/gemini-movie-detectives-ui](https://github.com/vojay-dev/gemini-movie-detectives-ui)

本文的重点是后端项目和基本概念。因此，它将简要解释前端及其组件。

在以下视频中，我还将概述该项目及其组件：

# 灵感

作为一个热衷于游戏的玩家，现在作为数据工程师工作，我一直被游戏和数据的交汇所吸引。通过这个项目，我结合了我最大的两个热情：游戏和数据。在90年代，我总是喜欢《你不知道杰克》系列视频游戏，这是一个有趣的知识问答和喜剧融合，不仅娱乐了我，还教会了我一些东西。通常，我对将游戏用于教育目的的概念也很着迷。

2023年，我组织了一个研讨会，教孩子和年轻人游戏开发。他们了解了碰撞检测背后的数学概念，但他们玩得很开心，因为一切都是在游戏的背景下展开的。令人大开眼界的是，游戏不仅是一个巨大的市场，而且还具有巨大的知识分享潜力。

通过这个名为电影侦探的项目，我旨在展示Gemini以及AI在打造引人入胜的知识问答和教育游戏方面的魔力，以及游戏设计如何从这些技术中受益。

通过将准确且最新的电影元数据提供给 Gemini LLM，我可以确保来自 Gemini 的问题准确无误。这一点非常重要，因为如果没有这种基于实时元数据的检索增强生成（RAG）方法来丰富查询，就有可能传播错误信息——这是使用 AI 进行此类任务时常见的陷阱。

另一个改变游戏规则的地方在于我使用 Jinja 模板创建的模块化提示生成框架。这就像拥有一把瑞士军刀，用于游戏设计——轻松切换主持人个性以定制游戏体验。通过语言模块，翻译问答成多种语言变得轻松，完全不需要昂贵的翻译过程。

从商业角度来看，它可以帮助接触到更广泛的客户群，无需昂贵的翻译过程。

从商业角度来看，这种模块化为更广泛的客户群打开了大门，轻松跨越语言障碍。此外，个人来说，我亲身体验了这些模块的变革力量。从默认的问答主持人切换到“老爸笑话问答主持人”简直太有趣了——这不仅是对《你不知道杰克》黄金时代的怀念，也是这个项目多功能性的见证。

![](../Images/44ed0a40a77f059c48e616639864e51e.png)

电影侦探——示例：圣诞老人性格（作者提供）

# 系统架构

在深入细节之前，让我们先了解一下应用是如何构建的。

**技术栈：🚀 后端**

+   Python 3.12 + [FastAPI](https://fastapi.tiangolo.com/) API 开发

+   [httpx](https://www.python-httpx.org/) 用于 TMDB 集成

+   [Jinja](https://jinja.palletsprojects.com/) 模板引擎用于模块化提示生成

+   [Pydantic](https://docs.pydantic.dev/latest/) 用于数据建模和验证

+   [Poetry](https://python-poetry.org/) 用于依赖管理

+   [Docker](https://www.docker.com/) 用于部署

+   [TMDB API](https://www.themoviedb.org/) 用于电影数据

+   [VertexAI](https://cloud.google.com/vertex-ai) 和 [Gemini](https://cloud.google.com/vertex-ai/docs/generative-ai/model-reference/gemini) 用于生成问答和评估答案

+   [Ruff](https://docs.astral.sh/ruff/) 作为代码检查器和代码格式化工具，结合 [pre-commit](https://pre-commit.com/) 钩子

+   使用 Github Actions 在每次推送时自动运行测试和代码检查

**技术栈：🖥️ 前端**

+   [VueJS](https://vuejs.org/) 3.4 作为前端框架

+   [Vite](https://vitejs.dev/) 用于前端工具链

本质上，应用从外部 API（TMDB）获取最新的电影元数据，根据不同的模块（如个性、语言等）构建提示，利用这些元数据丰富提示，从而使用 Gemini 发起一个电影问答，用户需要猜出正确的电影名称。

后端基础设施是通过 FastAPI 和 Python 构建的，采用检索增强生成（RAG）方法，通过实时元数据丰富查询。利用 Jinja 模板，后端将提示生成模块化为基础、个性和数据增强模板，从而能够生成准确且富有吸引力的测验问题。

前端由 Vue 3 和 Vite 提供支持，并由 daisyUI 和 Tailwind CSS 支持，便于高效的前端开发。这些工具共同为用户提供了一个现代化且流畅的界面，以便与后端进行无缝互动。

在《电影侦探》中，测验答案会再次由语言模型（LLM）进行解释，从而实现动态评分和个性化响应。这展示了将 LLM 与 RAG 集成在游戏设计和开发中的潜力，为真正个性化的游戏体验铺平了道路。此外，它还展示了通过引入 LLM 创建引人入胜的问答或教育类游戏的潜力。添加和更改个性或语言就像添加更多 Jinja 模板模块一样简单。通过非常少的工作，就可以改变整个游戏体验，减少开发人员的工作量。

![](../Images/b33ff35fa8348020b98001d766fc0b0f.png)

系统概述（作者提供）

正如概述所示，检索增强生成（RAG）是后端的核心思想之一。让我们更仔细地看看这一特定范式。

# 理解检索增强生成（RAG）

在大型语言模型（LLM）和人工智能领域，越来越流行的一种范式是检索增强生成（RAG）。那么 RAG 包括了什么内容，它又是如何影响人工智能发展的格局的呢？

本质上，RAG 通过结合外部数据来增强 LLM 系统，从而丰富它们的预测。这意味着，你将相关的上下文作为提示的附加部分传递给 LLM，但如何找到相关的上下文呢？通常，这些数据可以通过向量搜索或专用的向量数据库从数据库中自动检索。向量数据库尤其有用，因为它们以一种方式存储数据，使得可以快速查询相似数据。然后，LLM 会基于查询和检索到的文档生成输出。

想象一下：你有一个能够根据给定提示生成文本的 LLM。RAG 更进一步，通过注入来自外部来源的额外上下文，例如最新的电影数据，来增强生成文本的相关性和准确性。

让我们来拆解一下 RAG 的关键组成部分：

+   **LLMs**：LLM 是 RAG 工作流的核心。这些模型在大量文本数据上进行训练，具备理解和生成类人文本的能力。

+   **用于上下文增强的向量索引**：RAG 的一个关键方面是使用向量索引，这些索引以 LLM 可理解的格式存储文本数据的嵌入。这些索引允许在生成过程中高效地检索相关信息。在本项目的上下文中，这可以是一个电影元数据的数据库。

+   **检索过程**：RAG 涉及根据给定的上下文或提示检索相关的文档或信息。这些检索到的数据作为 LLM 的额外输入，补充其理解并提升生成回应的质量。这可能是获取所有与特定电影相关的已知信息。

+   **生成输出**：通过将 LLM 和检索到的上下文的知识结合，系统生成的文本不仅连贯，而且在上下文上相关，这得益于增强的数据。

![](../Images/256066ba8bd31d76d97009fb106ed7d4.png)

RAG 架构（作者提供）

而在 Gemini 电影侦探项目中，提示通过来自电影数据库（The Movie Database）的外部 API 数据进行了增强，RAG 通常涉及使用向量索引来简化这个过程。它使用更复杂的文档以及更大量的数据来进行增强。因此，这些索引像路标一样，引导系统迅速找到相关的外部数据源。

在这个项目中，实际上是 RAG 的一个迷你版，但至少展示了基本的思路，展示了外部数据如何增强 LLM 的能力。

更一般来说，RAG 是一个非常重要的概念，特别是在使用像 Gemini 这样的 LLM 构建趣味问答或教育游戏时。这个概念可以避免出现误报、提问错误的问题或误解用户的答案。

以下是一些开源项目，在你处理项目中的 RAG 时可能会有帮助：

+   [**txtai**](https://github.com/neuml/txtai): 一体化开源嵌入数据库，用于语义搜索、LLM 协同和语言模型工作流。

+   [**LangChain**](https://python.langchain.com/): LangChain 是一个用于开发由大型语言模型（LLMs）驱动的应用程序的框架。

+   [**Qdrant**](https://github.com/qdrant/qdrant): 面向下一代人工智能应用的向量搜索引擎。

+   [**Weaviate**](https://github.com/weaviate/weaviate): Weaviate 是一个云原生、开源的向量数据库，具有强大的性能、快速的处理速度和可扩展性。

当然，考虑到这种方法在 LLM 应用中的潜在价值，还有更多的开源和闭源替代方案，但通过这些，你应该能开始在该主题上的研究。

# 使用 Poetry 的 Python 项目

现在主要概念已经清晰，我们来仔细看看这个项目是如何创建的，以及依赖关系是如何管理的。

Poetry可以帮助你完成的三个主要任务是：构建、发布和跟踪。其理念是以一种确定性的方式管理依赖关系，分享你的项目，并跟踪依赖状态。

![](../Images/989ff4a10f32ee19b1a7a4fcd1be4171.png)

图片来源：[Kat von Wood](https://unsplash.com/@kat_von_wood?utm_source=medium&utm_medium=referral) 于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Poetry还会为你处理虚拟环境的创建。默认情况下，它们位于你系统中的一个集中文件夹内。不过，如果你更喜欢将项目的虚拟环境放在项目文件夹中，像我一样，只需进行简单的配置更改：

```py
poetry config virtualenvs.in-project true
```

使用`poetry new`，你可以创建一个新的Python项目。它会创建一个虚拟环境，并链接到系统默认的Python。如果你将此与[pyenv](https://github.com/pyenv/pyenv)结合使用，你可以灵活地使用特定版本创建项目。或者，你也可以直接告诉Poetry使用哪个Python版本：`poetry env use /full/path/to/python`。

一旦你有了一个新项目，可以使用`poetry add`命令将依赖项添加到其中。

通过这个，我为“双子座电影侦探”创建了项目：

```py
poetry config virtualenvs.in-project true
poetry new gemini-movie-detectives-api

cd gemini-movie-detectives-api

poetry add 'uvicorn[standard]'
poetry add fastapi
poetry add pydantic-settings
poetry add httpx
poetry add 'google-cloud-aiplatform>=1.38'
poetry add jinja2
```

关于你的项目的元数据，包括各个依赖项及其版本，都会存储在`poetry.toml`和`poetry.lock`文件中。我稍后添加了更多的依赖项，导致项目的`poetry.toml`文件如下所示：

```py
[tool.poetry]
name = "gemini-movie-detectives-api"
version = "0.1.0"
description = "Use Gemini Pro LLM via VertexAI to create an engaging quiz game incorporating TMDB API data"
authors = ["Volker Janz <volker@janz.sh>"]
readme = "README.md"

[tool.poetry.dependencies]
python = "^3.12"
fastapi = "^0.110.1"
uvicorn = {extras = ["standard"], version = "^0.29.0"}
python-dotenv = "^1.0.1"
httpx = "^0.27.0"
pydantic-settings = "^2.2.1"
google-cloud-aiplatform = ">=1.38"
jinja2 = "^3.1.3"
ruff = "^0.3.5"
pre-commit = "^3.7.0"

[build-system]
requires = ["poetry-core"]
build-backend = "poetry.core.masonry.api"
```

# 使用FastAPI创建API

FastAPI是一个Python框架，可以快速开发API。它建立在开放标准之上，提供无缝的体验，无需学习新的语法。通过自动生成文档、强大的验证功能和集成的安全性，FastAPI简化了开发过程，同时确保了卓越的性能。

![](../Images/65d620d2f1c52e0d1241ecb8e0fbd0b8.png)

图片来源：[Florian Steciuk](https://unsplash.com/@flo_stk?utm_source=medium&utm_medium=referral) 于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

实现“双子座电影侦探”项目的API时，我从一个Hello World应用程序开始，并从那里扩展。下面是如何开始的：

```py
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"Hello": "World"}
```

假设你也将虚拟环境保存在项目文件夹中，路径为`.venv/`，并使用uvicorn，以下是如何启动API并启用自动重载功能，以便在不需要重启的情况下测试代码更改：

```py
source .venv/bin/activate
uvicorn gemini_movie_detectives_api.main:app --reload
curl -s localhost:8000 | jq .
```

如果你还没有安装[jq](https://jqlang.github.io/jq/)，我强烈推荐你现在安装。我可能会在未来的文章中介绍这个神奇的JSON瑞士军刀。以下是响应的样子：

![](../Images/154da389d897c5178844dd5d3a349398.png)

Hello FastAPI（作者）

从这里开始，你可以根据需要开发API端点。例如，以下是实现启动电影问答的API端点的样子：

```py
@app.post('/quiz')
@rate_limit
@retry(max_retries=settings.quiz_max_retries)
def start_quiz(quiz_config: QuizConfig = QuizConfig()):
    movie = tmdb_client.get_random_movie(
        page_min=_get_page_min(quiz_config.popularity),
        page_max=_get_page_max(quiz_config.popularity),
        vote_avg_min=quiz_config.vote_avg_min,
        vote_count_min=quiz_config.vote_count_min
    )

    if not movie:
        logger.info('could not find movie with quiz config: %s', quiz_config.dict())
        raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail='No movie found with given criteria')

    try:
        genres = [genre['name'] for genre in movie['genres']]

        prompt = prompt_generator.generate_question_prompt(
            movie_title=movie['title'],
            language=get_language_by_name(quiz_config.language),
            personality=get_personality_by_name(quiz_config.personality),
            tagline=movie['tagline'],
            overview=movie['overview'],
            genres=', '.join(genres),
            budget=movie['budget'],
            revenue=movie['revenue'],
            average_rating=movie['vote_average'],
            rating_count=movie['vote_count'],
            release_date=movie['release_date'],
            runtime=movie['runtime']
        )

        chat = gemini_client.start_chat()

        logger.debug('starting quiz with generated prompt: %s', prompt)
        gemini_reply = gemini_client.get_chat_response(chat, prompt)
        gemini_question = gemini_client.parse_gemini_question(gemini_reply)

        quiz_id = str(uuid.uuid4())
        session_cache[quiz_id] = SessionData(
            quiz_id=quiz_id,
            chat=chat,
            question=gemini_question,
            movie=movie,
            started_at=datetime.now()
        )

        return StartQuizResponse(quiz_id=quiz_id, question=gemini_question, movie=movie)
    except GoogleAPIError as e:
        raise HTTPException(status_code=status.HTTP_500_INTERNAL_SERVER_ERROR, detail=f'Google API error: {e}')
    except Exception as e:
        raise HTTPException(status_code=status.HTTP_500_INTERNAL_SERVER_ERROR, detail=f'Internal server error: {e}')
```

在这段代码中，你可以看到后端的三个主要组件：

+   `tmdb_client`：我使用 `httpx` 实现的客户端，用于从电影数据库（TMDB）获取数据。

+   `prompt_generator`：一个帮助基于 Jinja 模板生成模块化提示的类。

+   `gemini_client`：一个通过 Google Cloud 的 VertexAI 与 Gemini LLM 交互的客户端。

我们稍后会详细讨论这些组件，但首先是一些关于 FastAPI 使用的有用见解。

FastAPI 使得定义 HTTP 方法和要传输到后端的数据变得非常简单。对于这个特定的函数，我期望一个 `POST` 请求，因为它创建了一个新的测验。这可以通过 `post` 装饰器来完成：

```py
@app.post('/quiz')
```

此外，我还期望请求中的数据以 JSON 格式发送在请求体内。在这种情况下，我期望 `QuizConfig` 的实例作为 JSON。 我简单地将 `QuizConfig` 定义为 Pydantic 的 `BaseModel` 子类（*稍后会介绍*），通过这样做，我可以将它传递给 API 函数，FastAPI 会处理其余部分：

```py
class QuizConfig(BaseModel):
    vote_avg_min: float = Field(5.0, ge=0.0, le=9.0)
    vote_count_min: float = Field(1000.0, ge=0.0)
    popularity: int = Field(1, ge=1, le=3)
    personality: str = Personality.DEFAULT.name
    language: str = Language.DEFAULT.name
# ...
def start_quiz(quiz_config: QuizConfig = QuizConfig()):
```

此外，你可能会注意到两个自定义装饰器：

```py
@rate_limit
@retry(max_retries=settings.quiz_max_retries)
```

我实现了这些功能来减少重复代码。它们包装了 API 函数，在发生错误时重试该函数，并引入了一个全局的速率限制，限制每天可以启动多少个电影测验。

我个人也很喜欢使用 FastAPI 进行错误处理。你可以简单地抛出一个`HTTPException`，指定所需的状态码，用户就会收到一个合适的响应，例如，如果在给定的配置下找不到电影：

```py
raise HTTPException(status_code=status.HTTP_404_NOT_FOUND, detail='No movie found with given criteria')
```

通过这一点，你应该能够概览如何使用 FastAPI 创建类似 Gemini Movie Detectives 的 API。记住：所有代码都是开源的，欢迎查看 [GitHub 上的 API 仓库](https://github.com/vojay-dev/gemini-movie-detectives-api)。

# 使用 Pydantic 进行数据验证和质量控制

当前的 AI/ML 项目面临的主要挑战之一是数据质量。但这不仅仅适用于 ETL/ELT 管道，这些管道准备的数据集用于模型训练或预测，也适用于 AI/ML 应用本身。例如，使用 Python 通常可以让数据工程师和科学家通过少量代码获得合理的结果，但由于 Python（大多数情况下）是动态类型的，若以天真方式使用，Python 缺乏数据验证。

这就是为什么在这个项目中，我将 FastAPI 与 Pydantic 结合使用，Pydantic 是一个强大的 Python 数据验证库。目标是使 API 轻量化，但在数据质量和验证方面严格且强大。例如，Movie Detectives API 严格使用从 Pydantic 提供的 `BaseModel` 继承的自定义类，而不是简单的字典。以下是一个小测验的配置示例：

```py
class QuizConfig(BaseModel):
    vote_avg_min: float = Field(5.0, ge=0.0, le=9.0)
    vote_count_min: float = Field(1000.0, ge=0.0)
    popularity: int = Field(1, ge=1, le=3)
    personality: str = Personality.DEFAULT.name
    language: str = Language.DEFAULT.name
```

这个示例说明了如何确保不仅是正确的类型，还应用了进一步的验证到实际值上。

此外，还使用了 Python 的最新特性，比如 `StrEnum`，用于区分某些类型，比如个性：

```py
class Personality(StrEnum):
    DEFAULT = 'default.jinja'
    CHRISTMAS = 'christmas.jinja'
    SCIENTIST = 'scientist.jinja'
    DAD = 'dad.jinja'
```

此外，通过定义自定义装饰器可以避免重复代码。例如，以下装饰器限制今天的测验会话次数，从而控制 GCP 成本：

```py
call_count = 0
last_reset_time = datetime.now()

def rate_limit(func: callable) -> callable:
    @wraps(func)
    def wrapper(*args, **kwargs) -> callable:
        global call_count
        global last_reset_time

        # reset call count if the day has changed
        if datetime.now().date() > last_reset_time.date():
            call_count = 0
            last_reset_time = datetime.now()

        if call_count >= settings.quiz_rate_limit:
            raise HTTPException(status_code=status.HTTP_400_BAD_REQUEST, detail='Daily limit reached')

        call_count += 1
        return func(*args, **kwargs)

    return wrapper
```

然后只需将其应用于相关的 API 函数：

```py
@app.post('/quiz')
@rate_limit
@retry(max_retries=settings.quiz_max_retries)
def start_quiz(quiz_config: QuizConfig = QuizConfig()):
```

结合了最新的 Python 特性和库，如 FastAPI、Pydantic 或 Ruff，使后端更加简洁，同时仍然非常稳定，确保一定的数据质量，从而确保 LLM 输出的质量符合预期。

# 使用 httpx 的 TMDB 客户端

TMDB 客户端类使用 [httpx](https://www.python-httpx.org/) 对 TMDB API 进行请求。

`httpx` 是 Python 库领域的冉冉升起的新星。尽管 `requests` 长期以来一直是进行 HTTP 请求的首选，但 `httpx` 提供了一个有效的替代方案。其一个关键优势是异步功能。`httpx` 允许你编写可以并发处理多个请求的代码，这可能会显著提高处理大量 HTTP 请求的应用程序的性能。此外，`httpx` 还致力于与 `requests` 的广泛兼容性，使开发人员更容易上手。

在 Gemini 电影侦探中，有两个主要请求：

+   `get_movies`：获取基于特定设置的随机电影列表，比如平均投票数。

+   `get_movie_details`：获取特定电影的详细信息，用于测验

为了减少外部请求的数量，后者使用 `lru_cache` 装饰器，即“最近最少使用缓存”。它用于缓存函数调用的结果，以便在相同的输入再次出现时，函数无需重新计算结果。相反，它会返回缓存的结果，这可以显著提高程序的性能，特别是对于计算开销较大的函数。在我们的例子中，我们缓存了 1024 部电影的详细信息，因此如果两位玩家获得相同的电影，我们就不需要再次发起请求：

```py
@lru_cache(maxsize=1024)
def get_movie_details(self, movie_id: int):
    response = httpx.get(f'https://api.themoviedb.org/3/movie/{movie_id}', headers={
        'Authorization': f'Bearer {self.tmdb_api_key}'
    }, params={
        'language': 'en-US'
    })

    movie = response.json()
    movie['poster_url'] = self.get_poster_url(movie['poster_path'])

    return movie
```

从电影数据库（TMDB）访问数据对于非商业用途是免费的，你只需 [生成一个 API 密钥](https://developer.themoviedb.org/docs/getting-started) 即可开始发起请求。

# Gemini LLM 客户端与 VertexAI

在使用通过 VertexAI 的 Gemini 之前，你需要一个启用了 VertexAI 的 Google Cloud 项目和一个具有足够权限的服务账户，以及其 JSON 密钥文件。

![](../Images/45142cdb2203e3f44a702c25a5395b06.png)

创建 GCP 项目（由作者）

创建新项目后，导航到 *APIs & Services* –> *Enable APIs and service* –> 搜索 *VertexAI API* –> *Enable*。

![](../Images/67fec39048c6afd73eb41b485431904b.png)

启用 VertexAI（由作者）

要创建服务账户，请导航到 *IAM & Admin* –> *Service Accounts* –> *Create service account*。选择一个合适的名称并进入下一步。

![](../Images/fc8a142ea8bcd0bd9da74ed24cd518e6.png)

创建服务账户（由作者）

现在，确保将账户分配给预定义角色 *Vertex AI User*。

![](../Images/3cfa198c9646a12a29bac51e718d2dba.png)

分配正确的角色（由作者提供）

最后，你可以通过点击新用户 –> *密钥* –> *添加密钥* –> *创建新密钥* –> *JSON* 来生成并下载JSON密钥文件。拥有这个文件后，你就可以开始使用了。

![](../Images/7e3e4f8e583b623603de57bb4a372e75.png)

创建JSON密钥文件（由作者提供）

通过VertexAI使用Google的Gemini与Python集成，首先需要将必要的依赖项添加到项目中：

```py
poetry add 'google-cloud-aiplatform>=1.38'
```

有了这个，你可以使用你的JSON密钥文件导入并初始化`vertexai`。你还可以加载一个模型，比如新发布的Gemini 1.5 Pro模型，并像这样开始一个聊天会话：

```py
import vertexai
from google.oauth2.service_account import Credentials
from vertexai.generative_models import GenerativeModel

project_id = "my-project-id"
location = "us-central1"

credentials = Credentials.from_service_account_file("credentials.json")
model = "gemini-1.0-pro"

vertexai.init(project=project_id, location=location, credentials=credentials)
model = GenerativeModel(model)

chat_session = model.start_chat()
```

现在，你可以使用`chat.send_message()`向模型发送提示。然而，由于你会收到分块的数据回复，我建议使用一个小助手函数，这样你就可以将完整的回复作为一个字符串获得：

```py
def get_chat_response(chat: ChatSession, prompt: str) -> str:
    text_response = []
    responses = chat.send_message(prompt, stream=True)
    for chunk in responses:
        text_response.append(chunk.text)
    return ''.join(text_response)
```

一个完整的示例可能看起来像这样：

```py
import vertexai
from google.oauth2.service_account import Credentials
from vertexai.generative_models import GenerativeModel, ChatSession

project_id = "my-project-id"
location = "us-central1"

credentials = Credentials.from_service_account_file("credentials.json")
model = "gemini-1.0-pro"

vertexai.init(project=project_id, location=location, credentials=credentials)
model = GenerativeModel(model)

chat_session = model.start_chat()

def get_chat_response(chat: ChatSession, prompt: str) -> str:
    text_response = []
    responses = chat.send_message(prompt, stream=True)
    for chunk in responses:
        text_response.append(chunk.text)
    return ''.join(text_response)

response = get_chat_response(
    chat_session,
    "How to say 'you are awesome' in Spanish?"
)
print(response)
```

运行此代码后，Gemini给出了以下回复：

![](../Images/cda38295b73a2e42d444a20a344055f2.png)

你真棒（由作者提供）

我同意Gemini的看法：

> ***你真了不起***

使用时的另一个提示：你还可以通过将配置传递给`send_message`函数中的`generation_config`参数来配置模型的生成。例如：

```py
generation_config = {
    'temperature': 0.5
}

responses = chat.send_message(
    prompt,
    generation_config=generation_config,
    stream=True
)
```

我在Gemini电影侦探中使用此方法将`temperature`设置为0.5，这给了我最佳的结果。在这个上下文中，`temperature`的意思是：Gemini生成的回复有多具创意。这个值必须介于0.0和1.0之间，值越接近1.0，表示创意越多。

除了发送提示并从Gemini接收回复之外，主要的挑战之一是解析回复以提取相关信息。

从这个项目中学到的一点是：

> ***为Gemini指定一种格式，不依赖于精确的词语，而是使用关键符号来分隔信息元素***

例如，Gemini的提问提示包含以下指令：

```py
Your reply must only consist of three lines! You must only reply strictly using the following template for the three lines:
Question: <Your question>
Hint 1: <The first hint to help the participants>
Hint 2: <The second hint to get the title more easily>
```

幼稚的做法是，通过查找以`Question:`开头的行来解析答案。然而，如果我们使用另一种语言，比如德语，回复会是：`Antwort:`。

相反，要关注结构和关键符号。像这样阅读回复：

+   它有3行

+   第一行是问题

+   第二行第一个提示

+   第三行第二个提示

+   键和值之间由`:`分隔

使用这种方法，回复可以无语言依赖地进行解析，这是我在实际客户端中的实现：

```py
@staticmethod
def parse_gemini_question(gemini_reply: str) -> GeminiQuestion:
    result = re.findall(r'[^:]+: ([^\n]+)', gemini_reply, re.MULTILINE)
    if len(result) != 3:
        msg = f'Gemini replied with an unexpected format. Gemini reply: {gemini_reply}'
        logger.warning(msg)
        raise ValueError(msg)

    question = result[0]
    hint1 = result[1]
    hint2 = result[2]

    return GeminiQuestion(question=question, hint1=hint1, hint2=hint2)
```

未来，回复的解析将变得更加简单。在2024年Google Cloud Next会议期间，Google宣布Gemini 1.5 Pro现已公开发布，并宣布了一些新特性，包括支持JSON模式，以便回复以JSON格式返回。有关更多细节，请查看[这篇文章](https://developers.googleblog.com/2024/04/gemini-15-pro-in-public-preview-with-new-features.html)。

除此之外，我将 Gemini 客户端封装成了一个可配置的类。你可以在 [Github 上查看完整的开源实现](https://github.com/vojay-dev/gemini-movie-detectives-api/blob/main/gemini_movie_detectives_api/gemini.py)。

# 使用 Jinja 的模块化提示生成器

提示生成器是一个类，结合并渲染 Jinja2 模板文件，创建模块化的提示。

有两个基本模板：一个用于生成问题，一个用于评估答案。除此之外，还有一个元数据模板，用于通过最新的电影数据丰富提示。进一步地，还有语言和个性模板，这些模板被组织在不同的文件夹中，每个选项都有一个模板文件。

![](../Images/1ca5d024d4a77205380c152045e2a02e.png)

提示生成器（作者）

使用 Jinja2 使得可以使用一些高级功能，比如模板继承，这在元数据中有使用。

这使得扩展这个组件变得容易，不仅可以添加更多的个性和语言选项，还可以将其提取为独立的开源项目，以便其他 Gemini 项目使用。

# 前端

Gemini 电影侦探的前端分为四个主要组件，并使用`vue-router`在它们之间进行导航。

Home 组件简单地显示欢迎信息。

测验组件展示测验本身，并通过`fetch`与 API 进行交互。要创建一个测验，它向`api/quiz`发送一个包含所需设置的 POST 请求。然后，后端根据用户设置选择一部随机电影，使用模块化提示生成器创建提示，利用 Gemini 生成问题和提示，最后将所有内容返回给组件，以便渲染测验。

此外，每个测验在后端都会分配一个会话 ID，并存储在一个有限的 LRU 缓存中。

为了调试，这个组件从`api/sessions`端点获取数据。该端点返回所有**活动**的会话。

该组件展示了服务的统计信息。然而，到目前为止，只有一种类别的数据被展示，即测验限制。为了限制 VertexAI 和 GCP 使用的成本，测验会话有一个每日限制，次日的第一个测验将重置该限制。数据通过`api/limit`端点获取。

![](../Images/260549abb360ab6e866e64b9ae1cd851.png)

Vue 组件（作者）

# API 示例

当然，使用前端是与应用交互的一个好方法，但也可以仅使用 API。

以下例子展示了如何通过 API 启动一个测验，使用圣诞老人/圣诞节个性：

```py
curl -s -X POST https://movie-detectives.com/api/quiz \
  -H 'Content-Type: application/json' \
  -d '{"vote_avg_min": 5.0, "vote_count_min": 1000.0, "popularity": 3, "personality": "christmas"}' | jq .
```

```py
{
  "quiz_id": "e1d298c3-fcb0-4ebe-8836-a22a51f87dc6",
  "question": {
    "question": "Ho ho ho, this movie takes place in a world of dreams, just like the dreams children have on Christmas Eve after seeing Santa Claus! It's about a team who enters people's dreams to steal their secrets. Can you guess the movie? Merry Christmas!",
    "hint1": "The main character is like a skilled elf, sneaking into people's minds instead of houses. ",
    "hint2": "I_c_p_i_n "
  },
  "movie": {...}
}
```

![](../Images/44ed0a40a77f059c48e616639864e51e.png)

电影侦探 — 示例：圣诞老人个性（作者）

这个例子展示了如何为测验更改语言：

```py
curl -s -X POST https://movie-detectives.com/api/quiz \
  -H 'Content-Type: application/json' \
  -d '{"vote_avg_min": 5.0, "vote_count_min": 1000.0, "popularity": 3, "language": "german"}' | jq .
```

```py
{
  "quiz_id": "7f5f8cf5-4ded-42d3-a6f0-976e4f096c0e",
  "question": {
    "question": "Stellt euch vor, es gäbe riesige Monster, die auf der Erde herumtrampeln, als wäre es ein Spielplatz! Einer ist ein echtes Urviech, eine Art wandelnde Riesenechse mit einem Atem, der so heiß ist, dass er euer Toastbrot in Sekundenschnelle rösten könnte. Der andere ist ein gigantischer Affe, der so stark ist, dass er Bäume ausreißt wie Gänseblümchen. Und jetzt ratet mal, was passiert? Die beiden geraten aneinander, wie zwei Kinder, die sich um das letzte Stück Kuchen streiten! Wer wird wohl gewinnen, die Riesenechse oder der Superaffe? Das ist die Frage, die sich die ganze Welt stellt! ",
    "hint1": "Der Film spielt in einer Zeit, in der Monster auf der Erde wandeln.",
    "hint2": "G_dz_ll_ vs. K_ng "
  },
  "movie": {...}
}
```

这是通过 API 调用回答测验的方法：

```py
curl -s -X POST https://movie-detectives.com/api/quiz/84c19425-c179-4198-9773-a8a1b71c9605/answer \
  -H 'Content-Type: application/json' \
  -d '{"answer": "Greenland"}' | jq .
```

```py
{
  "quiz_id": "84c19425-c179-4198-9773-a8a1b71c9605",
  "question": {...},
  "movie": {...},
  "user_answer": "Greenland",
  "result": {
    "points": "3",
    "answer": "Congratulations! You got it! Greenland is the movie we were looking for. You're like a human GPS, always finding the right way!"
  }
}
```

# 结论

在我完成基本项目后，通过模块化提示方法轻松添加更多个性和语言，我对游戏设计和开发的可能性感到印象深刻。通过添加另一个个性，我可以在一分钟内将这个游戏从一个关于电影的纯教育游戏变成一个喜剧问答类似于“You Don’t Know Jack”的游戏。

此外，将最新的Python功能与Pydantic等验证库结合起来非常强大，可用于确保LLM输入的数据质量良好。

以上就是，朋友们！现在你已经具备了打造自己的LLM驱动网络应用的能力。

感到灵感迸发但需要一个起点吗？查看Gemini Movie Detectives项目的开源代码：

+   **🚀 后端Github仓库**: [https://github.com/vojay-dev/gemini-movie-detectives-api](https://github.com/vojay-dev/gemini-movie-detectives-api)

+   **🖥️ 前端Github仓库**: [https://github.com/vojay-dev/gemini-movie-detectives-ui](https://github.com/vojay-dev/gemini-movie-detectives-ui)

人工智能驱动应用的未来光明无限，而你就是那位拿着画笔的人！让我们一起创造一些非凡的东西。如果你需要休息，可以尝试一下[https://movie-detectives.com/](https://movie-detectives.com/)。
