# 任务感知 RAG 策略：当句子相似性失效时的应对方法

> 原文：[https://towardsdatascience.com/task-aware-rag-strategies-for-when-sentence-similarity-fails-54c44690fee3?source=collection_archive---------2-----------------------#2024-06-10](https://towardsdatascience.com/task-aware-rag-strategies-for-when-sentence-similarity-fails-54c44690fee3?source=collection_archive---------2-----------------------#2024-06-10)

## 改善超越语义相似性的检索

[](https://medium.com/@aimichael?source=post_page---byline--54c44690fee3--------------------------------)[![Michael Ryaboy](../Images/783a6c1ed59277776003aaeab69f0b07.png)](https://medium.com/@aimichael?source=post_page---byline--54c44690fee3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--54c44690fee3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--54c44690fee3--------------------------------) [Michael Ryaboy](https://medium.com/@aimichael?source=post_page---byline--54c44690fee3--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--54c44690fee3--------------------------------) ·16 分钟阅读·2024年6月10日

--

![](../Images/29b772c537ea929ba301f21b378a2693.png)

图片由 [Joshua Golde](https://unsplash.com/@joshgmit) 提供，来源于 [Unsplash](https://unsplash.com/photos/white-stage-qIu77BsFdds)

向量数据库通过允许我们嵌入数据并使用相同的嵌入模型快速搜索，彻底改变了我们搜索和检索信息的方式，在推理时只有查询被嵌入。然而，尽管向量数据库具有令人印象深刻的能力，但它们也存在一个根本性缺陷：它们将查询和文档视为相同的。这可能导致次优的结果，尤其是在处理复杂任务如匹配时，因为查询和文档本质上是不同的。

任务感知 RAG（检索增强生成）的挑战在于其不仅需要基于语义相似性来检索文档，还需要结合额外的上下文指令。这为检索过程增加了一层复杂性，因为它必须考虑多个相关性维度。

## 这里有一些任务感知 RAG 问题的示例：

**1\. 将公司问题陈述与求职者匹配**

+   查询：“寻找具有可扩展系统设计经验并且在优化大规模数据库方面有成功记录的候选人，适合应对我们当前的挑战：在现有基础设施上将数据检索速度提高30%。”

+   上下文：该查询旨在直接将公司的具体技术挑战与具备相关技能和经验的潜在求职者联系起来。

**2\. 将伪域名与创业公司描述匹配**

+   查询： “为一家专注于AI驱动的个性化学习平台的高中生创业公司匹配伪域名，强调互动式和自适应学习技术。”

+   上下文：旨在找到一个适合且引人注目的伪域名，反映出创业公司的创新和教育重点。伪域名是基于伪词的域名，伪词是听起来真实但并非真实的词语。

**3\. 投资者与创业公司配对**

+   查询： “识别对早期生物技术创业公司感兴趣的投资者，重点关注个性化医学，并且有支持医疗行业种子轮投资的历史。”

+   上下文：该查询旨在匹配生物技术领域的创业公司，特别是那些从事个性化医学的公司，与那些不仅对生物技术感兴趣，而且曾在类似阶段和行业进行过投资的投资者。

**4\. 检索特定类型的文档**

+   查询： “检索最近的研究论文和案例研究，讨论区块链技术在保障数字投票系统中的应用，重点关注在美国或欧洲选举中测试的解决方案。”

+   上下文：明确指出需要对区块链某一特定应用提供学术和实践洞察，突出地理相关性和最近的应用案例

## 挑战

让我们考虑一个情景，其中一家公司面临各种问题，我们希望将这些问题与最相关的职位候选人匹配，这些候选人拥有解决这些问题所需的技能和经验。以下是一些示例问题：

1.  “高员工流动率正在促使对核心价值观和战略目标进行重新评估。”

    2\. “决策不透明的印象正在影响公司内部的信任水平。”

    3\. “远程培训课程缺乏参与度表明需要更具动态性的内容呈现方式。”

我们可以使用大语言模型生成每个问题的真实正样本和硬负样本候选。例如：

```py
problem_candidates = {
 "High employee turnover is prompting a reassessment of core values and strategic objectives.": {
 "True Positive": "Initiated a company-wide cultural revitalization project that focuses on autonomy and purpose to enhance employee retention.",
 "Hard Negative": "Skilled in rapid recruitment to quickly fill vacancies and manage turnover rates."
 },
 # … (more problem-candidate pairs)
}
```

尽管硬负样本表面上可能看起来相似，并且在嵌入空间中与查询更接近，但真实的正样本显然更适合解决特定问题。

## 解决方案：指令调优嵌入、重新排序和大语言模型（LLMs）

为了解决这个挑战，我们提出了一种多步骤的方法，结合了指令调优嵌入、重新排序和大语言模型（LLMs）：

## 1\. 指令调优嵌入

指令调优嵌入类似于双编码器，在该模型中，查询和文档的嵌入分别处理，然后对它们的嵌入进行比较。通过向每个嵌入提供额外的指令，我们可以将它们带入一个新的嵌入空间，在这个空间中，它们能够更有效地进行比较。

指令调优嵌入的关键优势在于，它们允许我们将特定的指令或上下文编码到嵌入本身。这在处理像职位描述与简历匹配这样的复杂任务时尤其有用，因为查询（职位描述）和文档（简历）的结构和内容是不同的。

通过在嵌入查询和文档之前添加特定任务的指令，我们理论上可以引导嵌入模型聚焦于相关方面并捕捉期望的语义关系。例如：

```py
documents_with_instructions = [
 "Represent an achievement of a job candidate achievement for retrieval: " + document 
 if document in true_positives 
 else document 
 for document in documents
]
```

该指令提示嵌入模型将文档表示为求职者成就，使其更适合根据给定的职位描述进行检索。

然而，RAG系统在没有评估的情况下很难解读，因此让我们编写一些代码来检查三种不同方法的准确性：

1\. 初级Voyage AI指令调优嵌入，没有额外指令。

2\. Voyage AI指令调优嵌入，包含额外的查询和文档上下文。

3\. Voyage AI非指令调优嵌入。

我们使用Voyage AI嵌入，因为它们目前是业内最顶尖的，并且在本文撰写时，稳居MTEB排行榜的顶部。我们还能够使用三种不同的策略，且向量维度相同，这将使得它们之间的比较更加容易。1024维度也恰好比任何接近于表现得如此优秀的嵌入模型小得多。

![](../Images/9041170cc2c5481c9ba3b1c1f4fe87b6.png)

MTEB排行榜

理论上，我们应该看到指令调优的嵌入在此任务上表现优于非指令调优的嵌入，即便只是因为它们在排行榜上排名较高。为了验证，我们将首先嵌入我们的数据。

当我们这样做时，我们尝试在文档前添加字符串：“表示求职者最相关的经验以便检索：”，这为我们的嵌入提供了更多关于文档的上下文。

如果你想跟随操作，请查看这个[colab链接](https://colab.research.google.com/drive/1iPy1WtOEPHU5YqEzHdiSpvUFL4JElGVJ?usp=sharing)。

```py
import voyageai

vo = voyageai.Client(api_key="VOYAGE_API_KEY")
```

```py
problems = []
true_positives = []
hard_negatives = []
for problem, candidates in problem_candidates.items():
    problems.append(problem)
    true_positives.append(candidates["True Positive"])
    hard_negatives.append(candidates["Hard Negative"])

documents = true_positives + hard_negatives
documents_with_instructions = ["Represent the most relevant experience of a job candidate for retrieval: " + document for document in documents]

batch_size = 50

resume_embeddings_naive = []
resume_embeddings_task_based = []
resume_embeddings_non_instruct  = []

for i in range(0, len(documents), batch_size):
    resume_embeddings_naive += vo.embed(
        documents[i:i + batch_size], model="voyage-large-2-instruct", input_type='document'
    ).embeddings

for i in range(0, len(documents), batch_size):
    resume_embeddings_task_based += vo.embed(
        documents_with_instructions[i:i + batch_size], model="voyage-large-2-instruct", input_type=None
    ).embeddings

for i in range(0, len(documents), batch_size):
    resume_embeddings_non_instruct += vo.embed(
        documents[i:i + batch_size], model="voyage-2", input_type='document' # we are using a non-instruct model to see how well it works
    ).embeddings
```

然后，我们将向量插入到向量数据库中。对于这个演示，我们严格来说不需要一个数据库，但具备元数据筛选功能的向量数据库将使代码更简洁，并最终能够扩展此测试。我们将使用KDB.AI，我是该平台的开发者倡导者。不过，任何具备元数据筛选功能的向量数据库都可以很好地工作。

要开始使用KDB.AI，请访问[kdb.ai](https://kdb.ai)以获取你的端点和API密钥。

然后，让我们实例化客户端并导入一些库。

```py
!pip install kdbai_client

import os
from getpass import getpass
import kdbai_client as kdbai
import time
```

通过我们的端点和API密钥连接到我们的会话。

```py
KDBAI_ENDPOINT = (
    os.environ["KDBAI_ENDPOINT"]
    if "KDBAI_ENDPOINT" in os.environ
    else input("KDB.AI endpoint: ")
)
KDBAI_API_KEY = (
    os.environ["KDBAI_API_KEY"]
    if "KDBAI_API_KEY" in os.environ
    else getpass("KDB.AI API key: ")
)

session = kdbai.Session(api_key=KDBAI_API_KEY, endpoint=KDBAI_ENDPOINT)
```

创建我们的表格：

```py
# We use the default database
database = session.database("default")

# Define the schema
schema = [
    {"name": "id", "type": "str"},
    {"name": "embedding_type", "type": "str"},
    {"name": "vectors", "type": "float64s"}  # Use float64s as per the migration guide
]

# Define the index
indexes = [
    {
        "name": "embedding_index",  # Name of the index
        "type": "flat",  # Index type
        "params": {"dims": 1024, "metric": "CS"},  # Specify the dimensions and metric
        "column": "vectors"  # Apply the index to the 'vectors' column
    }
]

# Create the table
table = database.create_table("data", schema=schema, indexes=indexes)
```

将候选成就插入到我们的索引中，并使用“embedding_type”元数据过滤器来区分我们的嵌入：

```py
import pandas as pd
embeddings_df = pd.DataFrame(
    {
        "id": documents + documents + documents,
        "embedding_type": ["naive"] * len(documents) + ["task"] * len(documents) + ["non_instruct"] * len(documents),
        "vectors": resume_embeddings_naive + resume_embeddings_task_based + resume_embeddings_non_instruct,
    }
)

table.insert(embeddings_df)
```

最后，评估上述三种方法：

```py
import numpy as np

# Function to embed problems and calculate similarity
def get_embeddings_and_results(problems, true_positives, model_type, tag, input_prefix=None):
    if input_prefix:
        problems = [input_prefix + problem for problem in problems]
    embeddings = vo.embed(problems, model=model_type, input_type="query" if input_prefix else None).embeddings

    # Retrieve most similar items
    results = []
    most_similar_items = table.search(
        vectors={"embedding_index": embeddings},
        n=1,
        filter=[("=", "embedding_type", tag)]
    )
    most_similar_items = np.array(most_similar_items)
    for i, item in enumerate(most_similar_items):
        most_similar = item[0][0] # the fist item
        results.append((problems[i], most_similar == true_positives[i]))
    return results

# Function to calculate and print results
def print_results(results, model_name):
    true_positive_count = sum([result[1] for result in results])
    percent_true_positives = true_positive_count / len(results) * 100
    print(f"\n{model_name} Model Results:")
    for problem, is_true_positive in results:
        print(f"Problem: {problem}, True Positive Found: {is_true_positive}")
    print("\nPercent of True Positives Found:", percent_true_positives, "%")

# Embedding, result computation, and tag for each model
models = [
    ("voyage-large-2-instruct", None, 'naive'),
    ("voyage-large-2-instruct", "Represent the problem to be solved used for suitable job candidate retrieval: ", 'task'),
    ("voyage-2", None, 'non_instruct'),
]

for model_type, prefix, tag in models:
    results = get_embeddings_and_results(problems, true_positives, model_type, tag, input_prefix=prefix)
    print_results(results, tag)
```

这是结果：

```py
naive Model Results:
Problem: High employee turnover is prompting a reassessment of core values and strategic objectives., True Positive Found: True
Problem: Perceptions of opaque decision-making are affecting trust levels within the company., True Positive Found: True
...
Percent of True Positives Found: 27.906976744186046 %

task Model Results:
...
Percent of True Positives Found: 27.906976744186046 %

non_instruct Model Results:
...
Percent of True Positives Found: 39.53488372093023 %
```

指令模型在这个任务上的表现更差！

我们的数据集足够小，差异并不显著（不到35个高质量的示例）。

尽管如此，这仍然表明

a) 单独的指令模型不足以处理这个具有挑战性的任务。

b) 虽然指令模型可以在类似任务上取得良好的表现，但始终进行评估是很重要的，因为在这个案例中，我曾怀疑它们会表现得更好，但事实并非如此。

c) 有一些任务对于指令模型来说效果较差。

## 2. 重新排序

虽然指令/常规嵌入模型可以在某种程度上缩小我们的候选范围，但显然我们需要更强大的模型，能够更好地理解文档之间的关系。

在使用指令调优的嵌入检索到初步结果后，我们使用一个交叉编码器（重新排序器）进一步优化排名。重新排序器考虑了特定的上下文和指令，从而使查询和检索到的文档之间的比较更加准确。

重新排序至关重要，因为它使我们能够以更细致的方式评估检索到的文档的相关性。与仅仅依赖查询和文档嵌入之间的相似性的初始检索步骤不同，重新排序会考虑查询和文档的实际内容。

通过联合处理查询和每个检索到的文档，重新排序器能够捕捉细粒度的语义关系，并更准确地确定相关性评分。这在初始检索可能返回表面上相似但实际上与特定查询无关的文档的场景中特别重要。

这是我们如何使用Cohere AI重新排序器进行重新排序的一个示例（Voyage AI也有一个很棒的重新排序器，但在我写这篇文章时，Cohere的表现更好。此后，他们推出了一个新的重新排序器，根据他们的内部基准测试，它的表现与之前一样，甚至更好。）

首先，让我们定义我们的重新排序函数。我们也可以使用Cohere的Python客户端，但我选择使用REST API，因为它似乎运行得更快。

```py
import requests
import json

COHERE_API_KEY = 'COHERE_API_KEY'

def rerank_documents(query, documents, top_n=3):

    # Prepare the headers
    headers = {
        'accept': 'application/json',
        'content-type': 'application/json',
        'Authorization': f'Bearer {COHERE_API_KEY}'
    }

    # Prepare the data payload
    data = {
        "model": "rerank-english-v3.0",
        "query": query,
        "top_n": top_n,
        "documents": documents,
        "return_documents": True
    }

    # URL for the Cohere rerank API
    url = 'https://api.cohere.ai/v1/rerank'

    # Send the POST request
    response = requests.post(url, headers=headers, data=json.dumps(data))

    # Check the response and return the JSON payload if successful
    if response.status_code == 200:
        return response.json()  # Return the JSON response from the server
    else:
        # Raise an exception if the API call failed
        response.raise_for_status()
```

现在，让我们评估我们的重新排序器。让我们还看看是否通过添加关于我们任务的额外上下文能提升性能。

```py
import cohere

co = cohere.Client('COHERE_API_KEY')
def perform_reranking_evaluation(problem_candidates, use_prefix):
    results = []

    for problem, candidates in problem_candidates.items():
        if use_prefix:
            prefix = "Relevant experience of a job candidate we are considering to solve the problem: "
            query = "Here is the problem we want to solve: " + problem
            documents = [prefix + candidates["True Positive"]] + [prefix + candidate for candidate in candidates["Hard Negative"]]
        else:
            query = problem
            documents = [candidates["True Positive"]]+ [candidate for candidate in candidates["Hard Negative"]]

        reranking_response = rerank_documents(query, documents)
        top_document = reranking_response['results'][0]['document']['text']
        if use_prefix:
            top_document = top_document.split(prefix)[1]

        # Check if the top ranked document is the True Positive
        is_correct = (top_document.strip() == candidates["True Positive"].strip())
        results.append((problem, is_correct))
        # print(f"Problem: {problem}, Use Prefix: {use_prefix}")
        # print(f"Top Document is True Positive: {is_correct}\n")

    # Evaluate overall accuracy
    correct_answers = sum([result[1] for result in results])
    accuracy = correct_answers / len(results) * 100
    print(f"Overall Accuracy with{'out' if not use_prefix else ''} prefix: {accuracy:.2f}%")

# Perform reranking with and without prefixes
perform_reranking_evaluation(problem_candidates, use_prefix=True)
perform_reranking_evaluation(problem_candidates, use_prefix=False)
```

现在，这里是我们的结果：

```py
Overall Accuracy with prefix: 48.84% 
Overall Accuracy without prefixes: 44.19%
```

通过添加关于我们任务的额外上下文，可能会提升重新排序的性能。我们还看到我们的重新排序器表现优于所有嵌入模型，即使没有额外的上下文，因此它应该被毫无疑问地加入到流水线中。不过，我们的性能还不理想，准确率低于50%（我们在不到50%的查询中首先检索到最佳结果），一定有方法可以做得更好！

重新排序器的最佳部分是它们开箱即用，但我们可以使用我们的黄金数据集（包含困难负例的示例）来[微调](https://docs.cohere.com/docs/rerank-starting-the-training)我们的重新排序器，使其更为准确。这可能大大提高我们的重新排序性能，但可能无法推广到不同类型的查询，并且每次输入变化时重新调整重新排序器可能会很令人沮丧。

## 3\. LLM

在重新排序后仍然存在歧义的情况下，可以利用 LLM 来分析检索到的结果并提供额外的上下文或生成定向总结。

LLM，如 GPT-4，能够根据给定的上下文理解和生成类人文本。通过将检索到的文档和查询输入 LLM，我们可以获得更细致的洞察力并生成量身定制的回答。

例如，我们可以使用 LLM 总结与查询相关的检索文档中最相关的方面，突出显示求职候选人的关键资质或经验，甚至根据匹配结果生成个性化的反馈或推荐。

这很好，因为它可以在结果传递给用户后进行，但如果我们想重新排序几十个或几百个结果呢？我们的 LLM 上下文将会超出限制，获取输出的时间也会变得过长。这并不意味着你不应该使用 LLM 来评估结果并向用户传递额外的上下文，但这确实意味着我们需要一个更好的最终步骤重新排序选项。

假设我们有一个如下所示的管道：

![](../Images/3d69f7309d972dfb1bdf22c6976abfb5.png)

简单管道

这个管道可以将数百万个可能的文档缩小到只有几十个。但是，最后的这几十个非常重要，我们可能只会传递三四个文档给 LLM！如果我们将一个求职候选人展示给用户，那么展示的第一个候选人比第五个更合适是非常重要的。

我们知道 LLM 是优秀的重新排序器，这有几个原因：

1.  **LLM 是了解列表的。** 这意味着它们可以看到其他候选项并进行比较，这是可以利用的附加信息。假设你（一个人类）被要求对一个候选人进行1到10分的评分，展示所有其他候选人会有帮助吗？当然有！

1.  **LLM 真是太聪明了。** LLM 理解它们所接到的任务，并且基于此可以非常有效地判断某个候选人是否合适，无论是简单的语义相似度如何。

我们可以通过基于困惑度的分类器来利用第二个原因。困惑度是一个度量，用来估计 LLM 对某个输出的‘困惑’程度。换句话说，我们可以让 LLM 将我们的候选项分类为‘非常合适’或‘不太合适’。根据它将候选项归为‘非常合适’的确定性（即该分类的困惑度），我们可以有效地对候选项进行排序。

有各种各样的优化可以进行，但在一个好的GPU上（强烈推荐使用GPU进行这一部分），我们可以在大约同样的时间内重新排序50个候选项，而cohere则能重新排序1000个。然而，我们可以在多个GPU上并行化此计算，从而加速这一过程，并扩展到重新排序数千个候选项。

首先，让我们安装并导入[lmppl](https://github.com/asahi417/lmppl)，一个可以帮助我们评估某些LLM完成度困惑度的库。我们还将创建一个评分器，这是一个大型T5模型（任何更大的模型运行得太慢，更小的模型表现较差）。如果你能通过解码器模型实现类似的结果，请告诉我，因为那样会更容易带来额外的性能提升（解码器的性能提升和成本下降速度远快于编码器-解码器模型）。

```py
!pip install lmppl
import lmppl

# Initialize the scorer for a encoder-decoder model, such as flan-t5\. Use small, large, or xl depending on your needs. (xl will run much slower unless you have a GPU and a lot of memory) I recommend large for most tasks.
scorer = lmppl.EncoderDecoderLM('google/flan-t5-large')
```

现在，让我们创建评估函数。这可以转化为任何重新排序任务的一般函数，或者你可以更改类别，看看是否能提高性能。这个例子似乎效果不错。我们缓存响应，以便重新运行相同的值时更快，但在GPU上这不是特别必要。

```py
cache = {}

def evaluate_candidates(query, documents, personality, additional_command=""):
    """
    Evaluate the relevance of documents to a given query using a specified scorer,
    caching individual document scores to avoid redundant computations.

    Args:
    - query (str): The query indicating the type of document to evaluate.
    - documents (list of str): List of document descriptions or profiles.
    - personality (str): Personality descriptor or model configuration for the evaluation.
    - additional_command (str, optional): Additional command to include in the evaluation prompt.

    Returns:
    - sorted_candidates_by_score (list of tuples): List of tuples containing the document description and its score, sorted by score in descending order.
    """
    try:
        uncached_docs = []
        cached_scores = []

        # Identify cached and uncached documents
        for document in documents:
            key = (query, document, personality, additional_command)
            if key in cache:
                cached_scores.append((document, cache[key]))
            else:
                uncached_docs.append(document)

        # Process uncached documents
        if uncached_docs:
            input_prompts_good_fit = [
                f"{personality} Here is a problem statement: '{query}'. Here is a job description we are determining if it is a very good fit for the problem: '{doc}'. Is this job description a very good fit? Expected response: 'a great fit.', 'almost a great fit', or 'not a great fit.' This document is: "
                for doc in uncached_docs
            ]

            print(input_prompts_good_fit)

            # Mocked scorer interaction; replace with actual API call or logic
            outputs_good_fit = ['a very good fit.'] * len(uncached_docs)
            # Calculate perplexities for combined prompts
            perplexities = scorer.get_perplexity(input_texts=input_prompts_good_fit, output_texts=outputs_good_fit)

            # Store scores in cache and collect them for sorting
            for doc, good_ppl in zip(uncached_docs, perplexities):
                score = (good_ppl)
                cache[(query, doc, personality, additional_command)] = score
                cached_scores.append((doc, score))

        # Combine cached and newly computed scores
        sorted_candidates_by_score = sorted(cached_scores, key=lambda x: x[1], reverse=False)

        print(f"Sorted candidates by score: {sorted_candidates_by_score}")

        print(query, ": ", sorted_candidates_by_score[0])

        return sorted_candidates_by_score

    except Exception as e:
        print(f"Error in evaluating candidates: {e}")
        return None
```

现在，让我们进行重新排序和评估：

```py
def perform_reranking_evaluation_neural(problem_candidates):
    results = []

    for problem, candidates in problem_candidates.items():
        personality = "You are an extremely intelligent classifier (200IQ), that effectively classifies a candidate into 'a great fit', 'almost a great fit' or 'not a great fit' based on a query (and the inferred intent of the user behind it)."
        additional_command = "Is this candidate a great fit based on this experience?"

        reranking_response = evaluate_candidates(problem, [candidates["True Positive"]]+ [candidate for candidate in candidates["Hard Negative"]], personality)
        top_document = reranking_response[0][0]

        # Check if the top ranked document is the True Positive
        is_correct = (top_document == candidates["True Positive"])
        results.append((problem, is_correct))
        print(f"Problem: {problem}:")
        print(f"Top Document is True Positive: {is_correct}\n")

    # Evaluate overall accuracy
    correct_answers = sum([result[1] for result in results])
    accuracy = correct_answers / len(results) * 100
    print(f"Overall Accuracy Neural: {accuracy:.2f}%")

perform_reranking_evaluation_neural(problem_candidates)
```

以及我们的结果：

```py
Overall Accuracy Neural: 72.09%
```

这比我们的重新排序器要好得多，而且不需要任何微调！不仅如此，它对任何任务都具有更大的灵活性，并且只需通过修改类别和提示工程就能更容易地获得性能提升。缺点是这种架构尚未优化，部署起来比较困难（我推荐使用[modal](http://modal.com)进行多GPU的无服务器部署，或者在VPS上部署GPU）。

有了这个神经任务感知的重新排序器，我们的工具箱就更强大了，我们可以创建一个更为稳健的重新排序管道：

![](../Images/1e06164e97d6d7f44554a6e3f5ecb7b4.png)

强大的多阶段重新排序管道

**结论**

增强文档检索对于复杂的匹配任务需要一种多方面的方法，利用不同AI技术的优势：

1\. **指令调优嵌入**通过编码特定任务的指令，为模型提供基础，以指导其捕捉查询和文档中的相关方面。然而，评估是验证其性能的关键。

2. **重新排序**通过深度分析内容相关性来优化检索结果。它可以受益于任务背景的额外信息。

3\. **基于LLM的分类器**作为强大的最终步骤，使得对顶级候选项进行细致的重新排序，从而以优化的顺序呈现最相关的结果，满足最终用户需求。

通过精心设计的指令调优嵌入、重排器和大型语言模型（LLMs），我们可以构建强大的AI管道，擅长解决诸如将求职者与职位要求匹配等挑战。细致的提示工程、顶尖的模型以及LLMs固有的能力，使得我们能够构建更高效的任务感知型RAG管道——在这种情况下，能够在将人们与理想机会对接方面取得卓越的成果。采用这种多角度的方法使我们能够构建不仅仅是检索语义相似文档，而是能够真正理解需求并找到符合我们独特需求的智能文档的检索系统。

在[LinkedIn](https://www.linkedin.com/in/michael-ryaboy-software-engineer/)上与我联系，获取更多AI工程技巧。
