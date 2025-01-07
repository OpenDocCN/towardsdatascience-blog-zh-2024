# 使用Prefect、Weave和RAGAS实现RAG应用的生产化

> 原文：[https://towardsdatascience.com/productionizing-a-rag-app-04c857e0966e?source=collection_archive---------7-----------------------#2024-08-03](https://towardsdatascience.com/productionizing-a-rag-app-04c857e0966e?source=collection_archive---------7-----------------------#2024-08-03)

## 添加评估、自动化数据提取及其他改进。

[](https://medium.com/@ed.izaguirre?source=post_page---byline--04c857e0966e--------------------------------)[![Ed Izaguirre](../Images/c9eded1f06c47571baa662107428483f.png)](https://medium.com/@ed.izaguirre?source=post_page---byline--04c857e0966e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--04c857e0966e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--04c857e0966e--------------------------------) [Ed Izaguirre](https://medium.com/@ed.izaguirre?source=post_page---byline--04c857e0966e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--04c857e0966e--------------------------------) ·阅读时长12分钟·2024年8月3日

--

![](../Images/105f6f4816994872796a36639b99148d.png)

从电影搜索到“玫瑰bud”🌹。图片来自Unsplash。

**目录**

1.  [介绍](#d4f8)

1.  [离线评估](#fdde)

1.  [在线评估](#9988)

1.  [使用Prefect进行自动化数据提取](#690c)

1.  [摘要](#0875)

**相关链接**

+   [GitHub仓库](https://github.com/EdIzaguirre/Rosebud)

+   [链接到之前讨论电影搜索的文章，这是该项目的开发版](https://medium.com/towards-data-science/how-to-build-a-rag-system-with-a-self-querying-retriever-in-langchain-16b4fa23e9ad)

+   [尝试该应用](https://filmsearch.azurewebsites.net/)（现在100%免费！🤑）

# 介绍

几个月前，我发布了*电影搜索*应用，这是一个基于用户查询推荐电影的检索增强生成（RAG）应用程序。例如，用户可能会问：“*帮我找到少于2小时、英语电影并且有狗的剧情片*。”然后会收到类似的推荐：

> 电影标题：《忠犬八公的故事》
> 
> 运行时：93分钟
> 
> 发行年份：2009
> 
> 流媒体：无法在线观看
> 
> 这部电影讲述了忠诚于主人的秋田犬八公的感人真实故事。电影深刻的情感表达以及友谊和忠诚的主题产生了强烈共鸣，使其成为一部感人的剧作，展现了人类与狗之间深厚的纽带。对于任何寻求一段突显陪伴重要性的真挚故事的人来说，这部电影都是完美的选择。
> 
> …

然而，这不仅仅是一个简单的 RAG 应用。它包括了被称为 **自查询检索** 的功能。这意味着机器人会获取用户的查询，并通过添加元数据过滤器来转换它。这确保了任何拉取到聊天模型上下文中的文档都遵守用户查询设置的约束条件。有关更多信息，建议查阅我之前的文章链接。

不幸的是，应用程序存在一些问题：

+   除了通过“眼睛测试”之外，没有进行离线评估。这个测试是必要的，但不足够。

+   可观察性几乎不存在。如果查询失败，你必须手动打开项目并运行一些临时脚本，试图查看出了什么问题。

+   Pinecone 向量数据库必须手动拉取。这意味着如果某部电影被从流媒体服务中下架，文档会迅速过时。

在本文中，我将简要介绍一些对电影搜索应用程序所做的改进。内容包括：

+   **使用 RAGAS 和 Weave 的离线评估**

+   **在线评估与可观察性**

+   **使用 Prefect 的自动化数据拉取**

在我们深入讨论之前，我发现“电影搜索”这个名字有些普通，因此我将应用重新命名为 *Rosebud* 🌹，因此上面显示的是该图像。真正的电影迷会 [理解这个引用](https://www.youtube.com/watch?v=O4mQqVqRB7I)。

# 离线评估

能够判断对 LLM 应用所做的更改是提升了性能还是降低了性能非常重要。不幸的是，LLM 应用的评估是一个复杂且全新的领域。对于什么构成一个好的评估，目前几乎没有一致的意见。

对于 Rosebud🌹，我决定处理被称为 [RAG 三合一方法](https://www.trulens.org/trulens_eval/getting_started/core_concepts/rag_triad/) 的问题。这种方法是由 TruLens 推广的，这是一个评估和跟踪 LLM 应用的平台。

![](../Images/d536e3e20215d54bdf6e350f4f06a994.png)

RAG 三合一方法。图片由作者提供。

三合一方法涵盖了 RAG 应用的三个方面：

+   **上下文相关性**：当用户发出查询时，文档填充了聊天模型的上下文。检索到的上下文实际上有用吗？如果没有，可能需要调整文档嵌入、分块或元数据过滤等方面。

+   **准确性**：模型的回答是否确实基于检索到的文档？你不希望模型编造事实；RAG 的关键就在于通过使用检索到的文档来减少幻觉。

+   **回答相关性**：模型的回答是否真的解答了用户的查询？如果用户询问“*1990年代的喜剧电影*”，模型的回答最好只包含1990年代的喜剧电影。

评估 RAG 应用的这三个功能有几种方式。一种方法是使用人类专家评估员。不幸的是，这样做会很昂贵，并且难以扩展。对于 Rosebud🌹，我决定使用 **LLMs 作为评审员**。这意味着使用聊天模型来查看上述三个标准中的每一个，并为每个标准打分，范围从 0 到 1。这种方法的优点是成本低且易于扩展。为此，我使用了 [RAGAS](https://github.com/explodinggradients/ragas)，这是一个流行的框架，帮助你评估 RAG 应用。RAGAS 框架包括上面提到的三个指标，并使你能够相对容易地使用它们来评估应用。下面是我进行离线评估时使用的代码片段：

```py
from ragas import evaluate
from ragas.metrics import AnswerRelevancy, ContextRelevancy, Faithfulness
import weave

@weave.op()
def evaluate_with_ragas(query, model_output):
    # Put data into a Dataset object
    data = {
        "question": [query],
        "contexts": [[model_output['context']]],
        "answer": [model_output['answer']]
    }
    dataset = Dataset.from_dict(data)

    # Define metrics to judge
    metrics = [
        AnswerRelevancy(),
        ContextRelevancy(),
        Faithfulness(),
    ]

    judge_model = ChatOpenAI(model=config['JUDGE_MODEL_NAME'])
    embeddings_model = OpenAIEmbeddings(model=config['EMBEDDING_MODEL_NAME'])

    evaluation = evaluate(dataset=dataset, metrics=metrics, llm=judge_model, embeddings=embeddings_model)

    return {
        "answer_relevancy": float(evaluation['answer_relevancy']),
        "context_relevancy": float(evaluation['context_relevancy']),
        "faithfulness": float(evaluation['faithfulness']),
    }

def run_evaluation():
    # Initialize chat model
    model = rosebud_chat_model()

    # Define evaluation questions
    questions = [
        {"query": "Suggest a good movie based on a book."},  # Adaptations
        {"query": "Suggest a film for a cozy night in."},  # Mood-Based
        {"query": "What are some must-watch horror movies?"},  # Genre-Specific
        ...
        # Total of 20 questions
    ]

    # Create Weave Evaluation object
    evaluation = weave.Evaluation(dataset=questions, scorers=[evaluate_with_ragas])

    # Run the evaluation
    asyncio.run(evaluation.evaluate(model))

if __name__ == "__main__":
    weave.init('film-search')
    run_evaluation()
```

几点说明：

+   有二十个问题和三个评判标准，你需要进行六十次 LLM 调用来完成一次评估！不过情况更糟；使用 `rosebud_chat_model` 时，每个查询需要两次调用：一次构建元数据过滤器，另一次提供答案，因此一次评估实际上需要 120 次调用！本次评估中使用的所有模型都是新的 `gpt-4o-mini`，我强烈推荐使用。在我的经验中，每次评估的调用费用为 $0.05。

+   请注意，我们使用了 `asyncio.run` 来运行评估。使用异步调用是理想的，因为你不想让每个问题按顺序一个接一个地评估。相反，使用 `asyncio` 时，我们可以在等待前一个 I/O 操作完成时，开始评估其他问题。

+   每次评估共有二十个问题。这些问题涵盖了用户可能提出的各种典型电影查询。我大部分是自己想出来的，但在实际应用中，最好使用生产环境中实际由用户提出的查询。

+   注意使用的 `weave.init` 和 `@weave.op` 装饰器。这些是来自 Weights & Biases (W&B) 的新 [Weave 库](https://wandb.ai/site/weave/) 的一部分。Weave 是传统 W&B 库的补充，专注于 LLM 应用。它通过简单的 `@weave.op` 装饰器，允许你捕获 LLM 的输入和输出。它还允许你使用 `weave.Evaluation(…)` 捕获评估结果。通过集成 RAGAS 进行评估，并使用 Weave 捕获和记录这些评估，我们得到了一个强大的组合，帮助 GenAI 开发者逐步改进他们的应用。你还可以记录模型的延迟、成本等信息。

![](../Images/ec7f61849ae276bbcc97508920e22e5b.png)

Weave + RAGAS 集成示例。图片由作者提供。

理论上，现在可以调整一个超参数（例如温度），重新运行评估，然后查看调整是否产生正面或负面影响。不幸的是，实际操作中我发现LLM判断有些挑剔，我[并非唯一一个](https://x.com/aparnadhinak/status/1748368364395721128)。LLM评判似乎很难使用浮点值来评估这些指标。相反，它们似乎在分类任务上表现得更好，例如赞或踩。RAGAS尚不支持LLM评判进行分类。手动编写这个功能似乎不难，也许在未来的更新中，我会尝试自己实现。

# **在线评估**

离线评估有助于查看调整超参数如何影响性能，但在我看来，在线评估要更有用。在Rosebud🌹中，我现在已经在每个响应的底部加入了👍/👎按钮来提供反馈。

![](../Images/c9351c7a2914be0e13e6295426c304fd.png)

在线反馈示例。图像由作者提供。

当用户点击任一按钮时，他们会被告知反馈已记录。以下是如何在 Streamlit 界面中实现这一功能的代码片段：

```py
def start_log_feedback(feedback):
    print("Logging feedback.")
    st.session_state.feedback_given = True
    st.session_state.sentiment = feedback
    thread = threading.Thread(target=log_feedback, args=(st.session_state.sentiment,
                                                         st.session_state.query,
                                                         st.session_state.query_constructor,
                                                         st.session_state.context,
                                                         st.session_state.response))
    thread.start()

def log_feedback(sentiment, query, query_constructor, context, response):
    ct = datetime.datetime.now()
    wandb.init(project="film-search",
               name=f"query: {ct}")
    table = wandb.Table(columns=["sentiment", "query", "query_constructor", "context", "response"])
    table.add_data(sentiment,
                   query,
                   query_constructor,
                   context,
                   response
                   )
    wandb.log({"Query Log": table})
    wandb.finish()
```

请注意，发送反馈到W&B的过程是通过一个独立的线程执行的，而不是在主线程上运行。这是为了避免用户在等待日志完成时被卡住。

使用W&B表格来存储反馈。表格中记录了五个数量：

+   **情感:** 用户点击了赞还是踩

+   **查询:** 用户的查询，例如 *找我一些英文的狗狗题材的剧情片，时长不到2小时。*

+   **查询构造器:** 查询构造器的结果，它重写了用户的查询，并在必要时包括元数据过滤，例如

```py
{
    "query": "drama English dogs", 
    "filter": {
        "operator": "and", 
        "arguments": [
            {
                "comparator": "eq", "attribute": "Genre", "value": "Drama"
            }, 
            {
                "comparator": "eq", "attribute": "Language", "value": "English"
            }, 

            {
                "comparator": "lt", "attribute": "Runtime (minutes)", "value": 120
            }
        ]
    },
}
```

+   **上下文:** 基于重构后的查询检索到的上下文，例如 *标题: 哈奇：一只狗的故事。概述: 基于一位大学教授真实故事的剧情片……*

+   **响应:** 模型的响应

所有这些都方便地记录在与先前展示的Weave评估相同的项目中。现在，当查询出错时，只需点击踩按钮，就可以看到具体发生了什么。这将大大加速Rosebud🌹推荐应用的迭代和改进。

![](../Images/f1ec5605b853d379a932d40b1012a4c2.png)

显示模型响应可观察性的图像。注意左侧如何在W&B和Weave之间无缝切换。图像由作者提供。

# **使用Prefect自动化数据拉取**

为了确保Rosebud🌹的推荐持续准确，自动化拉取数据并上传到Pinecone的过程变得尤为重要。为此，我选择了[Prefect](https://www.prefect.io/)。Prefect是一个流行的工作流编排工具。我正在寻找一个轻量、易学且符合Python风格的工具，而Prefect正符合这些要求。

![](../Images/74bf21e667d7580aa0f0a410a2ac3d9f.png)

Prefect 提供的自动化流程用于拉取和更新 Pinecone 向量存储。图片由作者提供。

Prefect 提供多种方式来调度您的工作流。我决定使用[带自动基础设施配置的推送工作池](https://docs.prefect.io/latest/tutorial/work-pools/#push-work-pools-with-automatic-infrastructure-provisioning)。我发现这种设置在简易性与可配置性之间达到了平衡。它允许用户将 Prefect 任务委托给自动配置运行您工作流所需的所有基础设施，部署到您选择的云提供商。我选择了在 Azure 上进行部署，但在 GCP 或 AWS 上部署只需更改几行代码。更多详细信息请参考 `pinecone_flow.py` 文件。以下是简化后的流程：

```py
@task
def start():
    """
    Start-up: check everything works or fail fast!
    """

    # Print out some debug info
    print("Starting flow!")

    # Ensure user has set the appropriate env variables
    assert os.environ['LANGCHAIN_API_KEY']
    assert os.environ['OPENAI_API_KEY']
    ...

@task(retries=3, retry_delay_seconds=[1, 10, 100])
def pull_data_to_csv(config):
    TMBD_API_KEY = os.getenv('TMBD_API_KEY')
    YEARS = range(config["years"][0], config["years"][-1] + 1)
    CSV_HEADER = ['Title', 'Runtime (minutes)', 'Language', 'Overview', ...]

    for year in YEARS:
        # Grab list of ids for all films made in {YEAR}
        movie_list = list(set(get_id_list(TMBD_API_KEY, year)))

        FILE_NAME = f'./data/{year}_movie_collection_data.csv'

        # Creating file
        with open(FILE_NAME, 'w') as f:
            writer = csv.writer(f)
            writer.writerow(CSV_HEADER)

        ...

    print("Successfully pulled data from TMDB and created csv files in data/")

@task
def convert_csv_to_docs():
    # Loading in data from all csv files
    loader = DirectoryLoader(
        ...
        show_progress=True)

    docs = loader.load()

    metadata_field_info = [
        AttributeInfo(name="Title",
                      description="The title of the movie", type="string"),
        AttributeInfo(name="Runtime (minutes)",
                      description="The runtime of the movie in minutes", type="integer"),
        ...
    ]

    def convert_to_list(doc, field):
        if field in doc.metadata and doc.metadata[field] is not None:
            doc.metadata[field] = [item.strip()
                                   for item in doc.metadata[field].split(',')]

    ...

    fields_to_convert_list = ['Genre', 'Actors', 'Directors',
                              'Production Companies', 'Stream', 'Buy', 'Rent']
    ...

    # Set 'overview' and 'keywords' as 'page_content' and other fields as 'metadata'
    for doc in docs:
        # Parse the page_content string into a dictionary
        page_content_dict = dict(line.split(": ", 1)
                                 for line in doc.page_content.split("\n") if ": " in line)

        doc.page_content = (
            'Title: ' + page_content_dict.get('Title') +
            '. Overview: ' + page_content_dict.get('Overview') +
            ...
        )

        ...

    print("Successfully took csv files and created docs")

    return docs

@task
def upload_docs_to_pinecone(docs, config):
    # Create empty index
    PINECONE_KEY, PINECONE_INDEX_NAME = os.getenv(
        'PINECONE_API_KEY'), os.getenv('PINECONE_INDEX_NAME')

    pc = Pinecone(api_key=PINECONE_KEY)

    # Target index and check status
    pc_index = pc.Index(PINECONE_INDEX_NAME)
    print(pc_index.describe_index_stats())

    embeddings = OpenAIEmbeddings(model=config['EMBEDDING_MODEL_NAME'])
    namespace = "film_search_prod"

    PineconeVectorStore.from_documents(
        docs,
        ...
    )

    print("Successfully uploaded docs to Pinecone vector store")

@task
def publish_dataset_to_weave(docs):
    # Initialize Weave
    weave.init('film-search')

    rows = []
    for doc in docs:
        row = {
            'Title': doc.metadata.get('Title'),
            'Runtime (minutes)': doc.metadata.get('Runtime (minutes)'),
             ...
        }
        rows.append(row)

    dataset = Dataset(name='Movie Collection', rows=rows)
    weave.publish(dataset)
    print("Successfully published dataset to Weave")

@flow(log_prints=True)
def pinecone_flow():
    with open('./config.json') as f:
        config = json.load(f)

    start()
    pull_data_to_csv(config)
    docs = convert_csv_to_docs()
    upload_docs_to_pinecone(docs, config)
    publish_dataset_to_weave(docs)

if __name__ == "__main__":
    pinecone_flow.deploy(
        name="pinecone-flow-deployment",
        work_pool_name="my-aci-pool",
        cron="0 0 * * 0",
        image=DeploymentImage(
            name="prefect-flows:latest",
            platform="linux/amd64",
        )
    )
```

注意将 Python 函数转换为 Prefect 流程是多么简单。您只需要一些使用 `@task` 装饰器的子函数，以及在主函数上使用 `@flow` 装饰器。还要注意，在将文档上传到 Pinecone 后，我们的流程的最后一步会将数据集发布到 Weave。这对于可重复性非常重要。要了解 Prefect 的基本知识，建议浏览[他们网站上的教程](https://docs.prefect.io/latest/tutorial/)。

在脚本的底部，我们可以看到如何在 Prefect 中进行部署。

+   我们需要为部署提供一个 `name` 。这个名称可以随意。

+   我们还需要指定一个 `work_pool_name` 。Prefect 中的推送工作池会自动将任务发送到无服务器计算机，而无需中介。这个名称需要与创建池时使用的名称匹配，下面我们将看到这一点。

+   您还需要指定一个 `cron` ，即计时器的简写。它允许您指定重复工作流的频率。值为 `"0 0 * * 0"` 意味着每周重复此工作流。有关 `cron` 语法如何工作的详细信息，请查看[这个网站](https://cloud.google.com/scheduler/docs/configuring/cron-job-schedules)。

+   最后，您需要指定一个 `DeploymentImage` 。在此处，您需要指定 `name` 和 `platform` 。名称可以随意，但平台不行。因为我想部署到 Azure 计算实例，并且这些实例运行 Linux，所以在 `DeploymentImage` 中指定这一点非常重要。

要使用 CLI 在 Azure 上部署此工作流，请运行以下命令：

```py
prefect work-pool create --type azure-container-instance:push --provision-infra my-aci-pool
prefect deployment run 'get_repo_info/my-deployment'
```

这些命令将自动配置 Azure 上所需的所有基础设施。其中包括一个 Azure 容器注册表 (ACR)，它将保存包含您目录中所有文件的 Docker 镜像以及在 `requirements.txt` 中列出的任何必要库。它还将包括一个 Azure 容器实例 (ACI) 身份，该身份将具有部署上述 Docker 镜像容器所需的权限。最后，`deployment run` 命令将每周调度一次代码运行。您可以查看 Prefect 仪表板，查看您的工作流运行情况：

![](../Images/fd1b54cdc71bbf012184a5f21cddd770.png)

Prefect流程成功运行的图像。图像由作者提供。

通过每周更新我的Pinecone向量存储，我可以确保Rosebud 🌹的推荐结果保持准确。

# 摘要

在本文中，我讨论了我在改进Rosebud 🌹应用程序方面的经验。这包括了整合离线和在线评估的过程，以及自动更新我的Pinecone向量存储。

其他一些未在本文中提及的改进：

+   在电影数据中加入了[电影数据库](https://www.themoviedb.org/?language=en-US)的评分。现在你可以要求“*高评分电影*”，聊天模型将筛选出评分高于7/10的电影。

+   升级版聊天模型。现在查询和摘要模型使用的是`gpt-4o-mini`。请记住，LLM 判断模型也在使用`gpt-4o-mini`。

+   嵌入模型已从`text-embedding-ada-002`升级为`text-embedding-3-small`。

+   现在的年份跨度为1950–2023年，而不是从1920年开始。1920–1950年的电影数据质量较差，只会导致推荐结果混乱。

+   用户界面更加简洁，所有项目相关的细节都被移到了侧边栏。

+   GitHub上的文档大幅改进。

+   错误修复。

如本文开头所述，该应用现在完全免费使用！我将在可预见的未来承担查询费用（因此选择了`gpt-4o-mini`而不是更昂贵的`gpt-4o`）。我非常希望能够获得运营一个生产环境应用的经验，让读者们试用Rosebud🌹是一个很好的方式。如果应用真的爆火，尽管这不太可能，我将需要找到其他的资金模式。但如果真有这种问题，那将是一个很好的问题。

享受发现精彩电影的乐趣！🎥
