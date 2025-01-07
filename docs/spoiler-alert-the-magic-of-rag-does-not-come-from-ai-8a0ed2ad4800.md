# 剧透警告：RAG的魔力并不来自AI

> 原文：[https://towardsdatascience.com/spoiler-alert-the-magic-of-rag-does-not-come-from-ai-8a0ed2ad4800?source=collection_archive---------0-----------------------#2024-11-17](https://towardsdatascience.com/spoiler-alert-the-magic-of-rag-does-not-come-from-ai-8a0ed2ad4800?source=collection_archive---------0-----------------------#2024-11-17)

## 为什么检索而非生成使RAG系统变得神奇

[](https://medium.com/@frankw_usa?source=post_page---byline--8a0ed2ad4800--------------------------------)[![Frank Wittkampf](../Images/3dbd69f8ef648074fa170fac451645fd.png)](https://medium.com/@frankw_usa?source=post_page---byline--8a0ed2ad4800--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8a0ed2ad4800--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8a0ed2ad4800--------------------------------) [Frank Wittkampf](https://medium.com/@frankw_usa?source=post_page---byline--8a0ed2ad4800--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8a0ed2ad4800--------------------------------) ·阅读时长8分钟·2024年11月17日

--

## 快速POC

大多数允许用户借助对话式AI探索数据的快速概念验证（POC）会让你惊叹不已。当你突然间能够与文档、数据或代码库进行对话时，那感觉就像是纯粹的魔法。

这些POC在小数据集和有限数量的文档中表现出色。然而，就像几乎所有东西一样，当你将它投入生产时，很快就会遇到规模化的问题。当你深入调查并检查AI给出的答案时，你会发现：

+   你的代理没有回复完整的信息。它漏掉了一些重要的数据。

+   你的代理并不能可靠地给出相同的答案。

+   你的代理无法告诉你它是如何以及从哪里获得这些信息的，这使得答案的实用性大大降低。

事实证明，**RAG中的真正魔力**并不发生在生成式AI步骤中，而是在检索和组合的过程中。一旦你深入了解，就会明白为什么…

** RAG = 检索增强生成 —* [*Wikipedia定义的RAG*](https://en.wikipedia.org/wiki/Retrieval-augmented_generation)

![](../Images/1546c06f10cd9e6f67fbd43ec17c1601.png)

RAG过程 — 插图

# 那么，一个启用RAG的AI代理是如何回答问题的呢？

简要回顾一下简单的RAG过程如何工作：

1.  一切都从**查询**开始。用户提出问题，或者某个系统正在尝试回答一个问题。

1.  **搜索**是通过查询来进行的。通常你会嵌入查询并进行相似度搜索，但你也可以进行经典的弹性搜索，或两者的结合，或者直接查找信息。

1.  搜索结果是一组**文档**（或文档片段，但我们暂且称之为文档）。

1.  文档和查询的要点被组合成易于阅读的**上下文**，以便AI能够使用它

1.  **AI解读**问题和文档，并生成答案

1.  理想情况下，这个答案会被**事实核对**，以查看AI是否是基于文档得出的答案，或者是否适合目标受众

# 魔法在哪里？

一个不为人知的秘密是，RAG流程的本质是你必须在AI做任何事情之前就向它提供答案，这样它才能给你你想要的回复。

换句话说：

+   AI所做的工作（步骤5）是**应用判断，并正确地表达答案**

+   工程师所做的工作（步骤3和4）是**找到答案并将其组合成AI能够理解的形式**

哪个更重要？答案当然是，视情况而定，因为如果判断是关键要素，那么AI模型就完成了所有的“魔法”。但是对于无数的业务用例来说，找到并正确组合构成答案的各个部分，才是更重要的部分。

# 如果你想要一个合适的RAG流程，典型的工程问题有哪些？

运行RAG流程时需要解决的第一个问题是数据摄取、拆分、分块和文档解析问题。我在[之前的文章](https://medium.com/@frankw_usa)中写过一些相关内容，但在这里不再提及。暂时假设你已经解决了数据摄取问题，拥有一个漂亮的向量存储或搜索索引。

典型挑战：

+   **重复性** — 即使是最简单的生产系统也常常会有重复文档。当系统规模很大，用户或租户众多，连接多个数据源，或处理版本控制等情况时，重复现象会更加严重。

+   **近似重复** — 文档中大部分内容相同，但有一些小的变化。近似重复有两种类型：

    — 有意义的 — 例如一个小的修正，或者一个小的添加，例如更新了日期字段

    — 无意义的 — 例如：小的标点符号、语法或空格差异，或者仅仅是由时间差或输入处理引起的差异

+   **数量** — 某些查询可能涉及到一个非常大的相关响应数据集

+   **数据的新鲜度与质量** — 哪些响应数据片段具有最高质量的内容供AI使用，而哪些片段则是从时间（新鲜度）角度最相关的？

+   **数据多样性** — 如何确保搜索结果多样性，以确保AI获得充分的信息？

+   **查询措辞与模糊性** — 触发RAG流程的提示，可能没有以一种能产生最佳结果的方式表达，或者可能本身就存在模糊性

+   **响应个性化** — 查询可能需要根据提问者不同而给出不同的回答

这个列表还可以继续，但你明白了大概意思。

# 旁注：无限上下文窗口不解决这个问题吗？

简短的回答：不。

使用极大上下文窗口的成本和性能影响不容小觑（你可能会将每次查询的成本提高10倍甚至100倍），更不用提用户/系统的任何后续交互。

然而，抛开这些不谈，想象一下以下情形。

> 我们让安妮进入一个房间，桌子上有一张纸。纸上写着：*病人乔：复杂性脚部骨折*。现在我们问安妮，病人是否有脚部骨折？她的回答是：“是的，他有”。
> 
> 现在我们给安妮提供了乔的100页病历记录。她的回答变成了：“嗯，这取决于你指的是哪个时间点，他曾经有过……”
> 
> 现在我们给安妮提供了关于诊所所有病人的成千上万页文档……

你很快会注意到，我们如何定义问题（或者在我们这里是提示）变得非常重要。**上下文窗口越大，查询所需的细节越多**。

此外，**上下文窗口越大，** **可能的答案范围也越广**。这可能是件好事，但实际上，这种方法容易引发懒惰的工程行为，如果处理不当，可能会削弱应用程序的能力。

# 推荐方法

当你将一个RAG系统从POC（概念验证）扩展到生产环境时，以下是如何通过具体解决方案应对典型数据挑战。

## 重复

在多来源系统中，重复是不可避免的。通过使用指纹识别（哈希内容）、文档ID或语义哈希，你可以在数据摄取时识别出完全重复的内容并防止冗余。然而，整合重复项之间的元数据也是有价值的；这可以让用户知道某些内容出现在多个来源中，这可能会增加可信度或突出数据集中的重复内容。

```py
# Fingerprinting for deduplication
def fingerprint(doc_content):
    return hashlib.md5(doc_content.encode()).hexdigest()

# Store fingerprints and filter duplicates, while consolidating metadata
fingerprints = {}
unique_docs = []
for doc in docs:
    fp = fingerprint(doc['content'])
    if fp not in fingerprints:
        fingerprints[fp] = [doc]
        unique_docs.append(doc)
    else:
        fingerprints[fp].append(doc)  # Consolidate sources
```

## 接近重复

接近重复的文档（相似但不完全相同）通常包含重要的更新或小的补充内容。考虑到像状态更新这样的微小变化可能包含关键信息，因此在过滤接近重复项时，新鲜度变得至关重要。一种实用方法是先使用余弦相似度进行初步检测，然后在每个接近重复的文档组中保留最新版本，同时标记任何有意义的更新。

```py
from sklearn.metrics.pairwise import cosine_similarity
from sklearn.cluster import DBSCAN
import numpy as np

# Cluster embeddings with DBSCAN to find near duplicates
clustering = DBSCAN(eps=0.1, min_samples=2, metric="cosine").fit(doc_embeddings)

# Organize documents by cluster label
clustered_docs = {}
for idx, label in enumerate(clustering.labels_):
 if label == -1:
 continue
 if label not in clustered_docs:
 clustered_docs[label] = []
 clustered_docs[label].append(docs[idx])

# Filter clusters to retain only the freshest document in each cluster
filtered_docs = []
for cluster_docs in clustered_docs.values():
 # Choose the document with the most recent timestamp or highest relevance
 freshest_doc = max(cluster_docs, key=lambda d: d['timestamp'])
 filtered_docs.append(freshest_doc)
```

## 数据量

当查询返回大量相关文档时，如何有效处理至关重要。一种方法是采用**分层策略**：

+   **主题提取**：预处理文档以提取特定的主题或摘要。

+   **Top-k过滤**：在合成后，基于相关性评分过滤摘要内容。

+   **相关性评分**：使用相似性度量（例如BM25或余弦相似度）在检索前优先排序最相关的文档。

这种方法通过检索经过合成的信息，减轻了工作负担，使得AI处理起来更为高效。其他策略可能包括按主题批量处理文档或预先对摘要进行分组，以进一步简化检索过程。

## 数据的新鲜度与质量

在快速变化的数据集中，平衡质量与新鲜度至关重要。虽然有多种评分方法，但这里有一种通用策略：

+   **复合评分**：通过源可靠性、内容深度和用户参与等因素计算质量得分。

+   **时效加权**：通过时间戳权重调整得分，以强调新鲜度。

+   **按阈值过滤**：只有满足质量和时效阈值的文档才会进行检索。

其他策略可能涉及仅对高质量来源评分或对较旧文档应用衰减因子。

## 数据多样性

确保检索中的数据来源多样化有助于创建平衡的响应。通过来源（例如，不同的数据库、作者或内容类型）对文档进行分组，并从每个来源选择最佳片段是一种有效方法。其他方法包括按独特视角评分或应用多样性约束，以避免过度依赖任何单一文档或视角。

```py
# Ensure variety by grouping and selecting top snippets per source

from itertools import groupby

k = 3  # Number of top snippets per source
docs = sorted(docs, key=lambda d: d['source'])

grouped_docs = {key: list(group)[:k] for key, group in groupby(docs, key=lambda d: d['source'])}
diverse_docs = [doc for docs in grouped_docs.values() for doc in docs]
```

## 查询措辞和模糊性

模糊查询可能导致次优的检索结果。直接使用用户的原始提示通常不是检索所需结果的最佳方式。例如，可能在聊天的早期有一个相关的信息交换，或者用户粘贴了一大段文本并提出了相关问题。

为确保使用精确的查询，一种方法是确保提供给模型的RAG工具要求其将问题重新表述为更详细的搜索查询，类似于人们为Google精心设计搜索查询的方式。这种方法提高了用户意图和RAG检索过程之间的对齐度。下面的措辞虽然不理想，但大致传达了意思：

```py
tools = [{ 
  "name": "search_our_database", 
  "description": "Search our internal company database for relevant documents", 
  "parameters": { 
   "type": "object", 
   "properties": { 
    "query": { 
     "type": "string", 
     "description": "A search query, like you would for a google search, in sentence form. Take care to provide any important nuance to the question." 
    } 
   }, 
   "required": ["query"] 
  } 
}]
```

## 响应个性化

为了提供量身定制的响应，直接将用户特定的上下文整合到RAG上下文组成中。通过在最终上下文中添加用户特定的层，允许AI考虑个人偏好、权限或历史记录，而不改变核心检索过程。

通过解决这些数据挑战，你的RAG系统可以从一个有说服力的概念验证（POC）进化为一个可靠的生产级解决方案。最终，RAG的有效性更多依赖于精心的工程设计，而不是AI模型本身。虽然AI可以生成流畅的答案，但真正的“魔力”在于我们如何检索和构建信息。因此，下次当你对AI系统的对话能力感到印象深刻时，请记住，这很可能是得益于背后精心设计的检索过程。

我希望这篇文章能为你提供一些关于RAG过程的见解，并解释为什么在与数据交谈时，你体验到的“魔力”不一定来自AI模型，而更多依赖于你的检索过程设计。

请留下评论，分享你的想法。
