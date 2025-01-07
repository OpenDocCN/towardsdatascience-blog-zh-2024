# 使用 MongoDB 构建 RAG 流水线：个性化推荐的向量搜索

> 原文：[`towardsdatascience.com/building-a-rag-pipeline-with-mongodb-vector-search-for-personalized-movie-picks-46a58a2aaac9?source=collection_archive---------8-----------------------#2024-08-01`](https://towardsdatascience.com/building-a-rag-pipeline-with-mongodb-vector-search-for-personalized-movie-picks-46a58a2aaac9?source=collection_archive---------8-----------------------#2024-08-01)

[](https://medium.com/@pablomerchanrivera?source=post_page---byline--46a58a2aaac9--------------------------------)![Pablo Merchán-Rivera, Ph.D.](https://medium.com/@pablomerchanrivera?source=post_page---byline--46a58a2aaac9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--46a58a2aaac9--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--46a58a2aaac9--------------------------------) [Pablo Merchán-Rivera, Ph.D.](https://medium.com/@pablomerchanrivera?source=post_page---byline--46a58a2aaac9--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--46a58a2aaac9--------------------------------) ·7 分钟阅读·2024 年 8 月 1 日

--

本文探讨了使用检索增强生成（RAG）流水线构建电影推荐系统的过程。目标是学习如何利用 MongoDB 的向量搜索功能，将数据描述转化为可搜索的数字指纹，并创建一个能够理解你偏好和沟通细节的系统。换句话说，我们的目标是构建一个不仅智能，而且高效的推荐系统。

在本文结束时，你将能够构建一个功能完整的电影推荐系统。该系统能够接受用户的查询，如 *“我想看一部探讨人工智能的科幻电影”* 或 *“什么是适合成人也能欣赏的好动画电影？为什么你的建议适合？”* 并返回相关的电影建议及选择理由。

![](img/a1cde82c3c844d5926591d0148cf5a4f.png)

图片由 [Alexandr Popadin](https://unsplash.com/@irrabagon?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 什么是 RAG 流水线？

RAG 管道指的是数据通过一系列处理步骤的顺序流动，结合了大型语言模型（LLM）与结构化数据检索的优势。它的工作原理是首先从知识库中检索相关信息，然后利用这些信息增强大型语言模型的输入，从而生成最终的输出。此类管道的主要目标是生成更准确、更具上下文相关性且更具个性化的响应，以回答用户针对庞大数据库提出的查询。

# 为什么选择 MongoDB？

MongoDB 是一个开源的 NoSQL 数据库，它以灵活的、类似 JSON 的文档形式存储数据，允许轻松扩展，并能处理多种数据类型和结构。MongoDB 在这个项目中扮演了重要角色。它的文档模型与我们的电影数据非常契合，而它的向量搜索功能可以对我们的嵌入（即电影内容的数字表示）进行相似度搜索。我们还可以利用索引和查询优化功能，以保持即使数据集扩展时也能快速检索数据。

# 我们的项目

我们的管道流程如下所示：

1.  设置环境并从 Hugging Face 加载电影数据

1.  使用 Pydantic 对数据进行建模

1.  为电影信息生成嵌入

1.  将数据导入 MongoDB 数据库

1.  在 MongoDB Atlas 中创建向量搜索索引

1.  执行向量搜索操作，找到相关电影

1.  使用 LLM 模型处理用户查询

1.  使用 RAG 管道获取电影推荐

## 第一步：设置环境并加载数据集

首先，我们需要导入必要的库并设置我们的环境。这还包括设置 API 密钥和应用程序用于连接 MongoDB 数据库的连接字符串：

```py
import warnings
warnings.filterwarnings('ignore')

import os
from dotenv import load_dotenv, find_dotenv
from datasets import load_dataset
import pandas as pd
from typing import List, Optional
from pydantic import BaseModel
from datetime import datetime
from pymongo.mongo_client import MongoClient
import openai
import time

_ = load_dotenv(find_dotenv())
MONGO_URI = os.environ.get("MONGO_URI")
OPENAI_API_KEY = os.environ.get("OPENAI_API_KEY")
openai.api_key = OPENAI_API_KEY
```

接下来，我们加载我们的电影数据集：

```py
dataset = load_dataset("Pablinho/movies-dataset", streaming=True, split="train")
dataset = dataset.take(200)  # 200 movies for the sake of simplicity
dataset_df = pd.DataFrame(dataset)
```

数据集包含超过 9000 条记录。然而，在这个练习中，我们将数据集限制为 200 部电影，使用`dataset.take(200)`。在实际应用中，您可能会使用更大的数据集。

## 第二步：使用 Pydantic 对数据建模

数据建模对于确保我们应用程序的一致性和类型安全至关重要。因此，我们使用 Pydantic 来实现这一目的：

```py
class Movie(BaseModel):
    Release_Date: Optional[str]
    Title: str
    Overview: str
    Popularity: float
    Vote_Count: int
    Vote_Average: float
    Original_Language: str
    Genre: List[str]
    Poster_Url: str
    text_embeddings: List[float]
```

使用 Pydantic 提供了多个好处，例如自动数据验证、类型检查和简便的序列化/反序列化。注意，我们还创建了一个`text_embeddings`字段，用于存储我们生成的嵌入，作为浮动点数列表。

## 第三步：嵌入生成

现在，我们可以使用 OpenAI API，并编写一个生成嵌入的函数，如下所示：

```py
def get_embedding(text):
    if not text or not isinstance(text, str):
        return None
    try:
        embedding = openai.embeddings.create(
            input=text,
            model="text-embedding-3-small", dimensions=1536).data[0].embedding
        return embedding
    except Exception as e:
        print(f"Error in get_embedding: {e}")
        return None
```

在之前的代码行中，我们首先检查输入是否有效（非空字符串）。然后，我们使用 OpenAI 的 embeddings.create 方法生成嵌入，采用“text-embedding-3-small”模型，该模型生成 1536 维的嵌入。

现在，我们可以处理每条记录并使用之前的函数生成嵌入。我们还添加了一些代码来处理 `'Genre'` 字段，将其从字符串（如果存在）转换为一组类型列表。

```py
def process_and_embed_record(record):
    for key, value in record.items():
        if pd.isnull(value):
            record[key] = None

    if record['Genre']:
        record['Genre'] = record['Genre'].split(', ')
    else:
        record['Genre'] = []

    text_to_embed = f"{record['Title']} {record['Overview']}"
    embedding = get_embedding(text_to_embed)
    record['text_embeddings'] = embedding
    return record

records = [process_and_embed_record(record) for record in dataset_df.to_dict(orient='records')]
```

这些嵌入将使我们能够进行语义搜索，找到与给定查询在概念上相似的电影。请注意，这一过程可能需要一些时间，尤其是在数据集较大的情况下，因为我们为每部电影都要进行一次 API 调用。

## 第 4 步：将数据导入 MongoDB

我们建立与 MongoDB 数据库的连接：

```py
def get_mongo_client(mongo_uri):
    client = MongoClient(mongo_uri, appname="pmr.movie.python")
    print("Connection to MongoDB successful")
    return client

mongo_client = get_mongo_client(MONGO_URI)
database_name = "movies_dataset"
collection_name = "movies"
db = mongo_client.get_database(database_name)
collection = db.get_collection(collection_name)

collection.delete_many({})
```

我们将处理并嵌入的数据插入 MongoDB，这使得我们能够高效地存储和查询我们的电影数据，包括高维嵌入：

```py
movies = [Movie(**record).dict() for record in records]
collection.insert_many(movies)
```

## 第 5 步：在 MongoDB Atlas 中创建向量搜索索引

在执行向量搜索操作之前，我们需要创建一个向量搜索索引。此步骤可以直接在 MongoDB Atlas 平台上完成：

1.  登录到您的 MongoDB Atlas 帐户

1.  导航到您的集群

1.  转到“搜索与向量搜索”标签

1.  点击“创建搜索索引”

1.  在“Atlas 向量搜索”部分选择“JSON 编辑器”，并使用以下配置：

```py
{
  "fields": [
    {
      "numDimensions": 1536,
      "path": "text_embeddings",
      "similarity": "cosine",
      "type": "vector"
    }
  ]
}
```

目标是创建一个名为 `"vector_index_text"` 的向量搜索索引，索引的字段为 `"text_embeddings"`。我们使用余弦相似度，因为它有助于通过比较嵌入向量的方向来找到主题或内容相似的电影，忽略长度或细节的差异，这对于将用户的查询与电影描述进行匹配非常有效。

## 第 6 步：实现向量搜索

现在，我们实现向量搜索功能。以下函数用于在我们的 MongoDB 集合中执行向量搜索。它首先为用户的查询生成嵌入。然后，利用 `$vectorSearch` 运算符构建 MongoDB 聚合管道。搜索会在 150 个候选项中查找 20 个最近的邻居。

```py
def vector_search(user_query, db, collection, vector_index="vector_index_text", max_retries=3):
    query_embedding = get_embedding(user_query)
    if query_embedding is None:
        return "Invalid query or embedding generation failed."

    vector_search_stage = {
        "$vectorSearch": {
            "index": vector_index,
            "queryVector": query_embedding,
            "path": "text_embeddings",
            "numCandidates": 150,
            "limit": 20
        }
    }

    pipeline = [vector_search_stage]

    for attempt in range(max_retries):
        try:
            results = list(collection.aggregate(pipeline))
            if results:
                explain_query_execution = db.command(
                    'explain', {
                        'aggregate': collection.name,
                        'pipeline': pipeline,
                        'cursor': {}
                    },
                    verbosity='executionStats')
                vector_search_explain = explain_query_execution['stages'][0]['$vectorSearch']
                millis_elapsed = vector_search_explain['explain']['collectStats']['millisElapsed']
                print(f"Total time for the execution to complete on the database server: {millis_elapsed} milliseconds")
                return results
            else:
                print(f"No results found on attempt {attempt + 1}. Retrying...")
                time.sleep(2)
        except Exception as e:
            print(f"Error on attempt {attempt + 1}: {str(e)}")
            time.sleep(2)

    return "Failed to retrieve results after multiple attempts."
```

我们实现了一个重试机制（最多 3 次尝试），以处理可能的临时问题。该函数还执行 `explain` 命令，提供有关查询执行的详细信息。

## 第 7 步：使用 LLM 处理用户查询

最后，我们可以处理用户查询。首先，我们定义一个 `SearchResultItem` 类来构建搜索结果。然后，`handle_user_query` 函数将所有内容结合在一起：它根据用户的查询执行向量搜索，将搜索结果格式化为 pandas DataFrame，并使用 OpenAI 的 GPT 模型（即 gpt-3.5-turbo）根据搜索结果和用户的查询生成回应，并显示结果及生成的回应：

```py
class SearchResultItem(BaseModel):
    Title: str
    Overview: str
    Genre: List[str]
    Vote_Average: float
    Popularity: float

def handle_user_query(query, db, collection):
    get_knowledge = vector_search(query, db, collection)

    if isinstance(get_knowledge, str):
        return get_knowledge, "No source information available."

    search_results_models = [SearchResultItem(**result) for result in get_knowledge]
    search_results_df = pd.DataFrame([item.dict() for item in search_results_models])

    completion = openai.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[
            {"role": "system", "content": "You are a movie recommendation system."},
            {"role": "user", "content": f"Answer this user query: {query} with the following context:\n{search_results_df}"}
        ]
    )

    system_response = completion.choices[0].message.content

    print(f"- User Question:\n{query}\n")
    print(f"- System Response:\n{system_response}\n")

    return system_response
```

这个功能实际上展示了 RAG 的核心价值：通过从我们的数据库中检索相关信息，生成一个情境适当的回应。

## 8\. 使用 RAG 管道

要使用此 RAG 管道，现在可以进行如下查询：

```py
query = """
I'm in the mood for a highly-rated action movie. Can you recommend something popular?
Include a reason for your recommendation.
"""
handle_user_query(query, db, collection)
```

系统将返回类似以下的响应：

```py
I recommend "Spider-Man: No Way Home" as a popular and highly-rated action 
movie for you to watch. With a vote average of 8.3 and a popularity score 
of 5083.954, this film has garnered a lot of attention and positive 
reviews from audiences. 

"Spider-Man: No Way Home" is a thrilling action-packed movie that brings 
together multiple iterations of Spider-Man in an epic crossover event. It 
offers a blend of intense action sequences, emotional depth, and nostalgic
moments that fans of the superhero genre will surely enjoy. So, if you're
in the mood for an exciting action movie with a compelling storyline and
fantastic visual effects, "Spider-Man: No Way Home" is an excellent choice
for your movie night.
```

# 结论

构建一个 RAG 管道涉及多个步骤，从数据加载和建模到嵌入生成和向量搜索。这个示例展示了如何通过将我们数据库中的特定电影数据与语言模型的自然语言理解和生成能力结合，来提供信息丰富、上下文感知的回答。在此基础上，我们使用 MongoDB，因为它具有原生向量搜索功能、灵活的文档模型和可扩展性，非常适合这种工作流程。

你可以通过添加更多数据、微调你的嵌入，或实现更复杂的推荐算法来扩展这个系统。

*有关完整代码和更多资源，请查看* [*GitHub 仓库*](https://github.com/mr-pablinho/rag-mongodb-moviepl)*。此项目使用的数据集来源于* [*Kaggle*](https://www.kaggle.com/datasets/disham993/9000-movies-dataset/data) *，并已获得原作者授予的 CC0 1.0 通用公共领域授权（CC0 1.0）。你可以在* [*这里*](https://huggingface.co/datasets/Pablinho/movies-dataset) *找到数据集和更多信息*。
