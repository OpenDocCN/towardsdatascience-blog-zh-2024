# 一种理解GraphRAG工作原理的简单方法

> 原文：[https://towardsdatascience.com/an-easy-way-to-comprehend-how-graphrag-works-6d53f8b540d0?source=collection_archive---------2-----------------------#2024-06-08](https://towardsdatascience.com/an-easy-way-to-comprehend-how-graphrag-works-6d53f8b540d0?source=collection_archive---------2-----------------------#2024-06-08)

## 帮助你理解GraphRAG机制的示例

[](https://medium.com/@rendysatriadalimunthe?source=post_page---byline--6d53f8b540d0--------------------------------)[![Rendy Dalimunthe](../Images/efcd8304211d187271847f4ecc5fb1b1.png)](https://medium.com/@rendysatriadalimunthe?source=post_page---byline--6d53f8b540d0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6d53f8b540d0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6d53f8b540d0--------------------------------) [Rendy Dalimunthe](https://medium.com/@rendysatriadalimunthe?source=post_page---byline--6d53f8b540d0--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6d53f8b540d0--------------------------------) ·6分钟阅读·2024年6月8日

--

开发一个能够应对实际问题并提供适当、准确答案的聊天机器人确实是一项艰巨的任务。尽管大型语言模型已经取得了显著进展，但一个开放的挑战是将这些模型与知识库结合，以提供可靠且具有上下文丰富的响应。

![](../Images/37b079cf812dd5c41040db84f5f3cbdb.png)

图片来源：[Google DeepMind](https://unsplash.com/@googledeepmind?utm_source=medium&utm_medium=referral) 于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

关键问题几乎总是归结于幻觉（模型生成错误或不存在的信息）和上下文理解，模型无法理解不同信息之间的微妙关系。其他人尝试构建强大的问答系统，但未取得太大成功，因为这些模型通常会返回不尽如人意的答案，尽管它们连接着全面的知识库。

尽管RAG可以通过将生成的响应与真实世界的数据连接来减少幻觉，但准确回答复杂问题却是另一回事。用户通常会看到类似“*该主题在检索的文本中未明确涉及*”的答案，即使知识库中明确包含该信息，尽管这种信息呈现得不那么显而易见。这正是GraphRAG（图检索增强生成）派上用场的地方，它通过利用结构化的知识图谱来提高模型提供精确且具有上下文丰富答案的能力。

**RAG：桥接检索与生成**

RAG 代表了结合基于检索和基于生成方法的最佳实践的重要一步。给定一个查询，RAG 从大型语料库中检索相关文档或段落，然后基于这些信息生成答案。因此，可以确保生成的文本既具有信息性又与上下文相关，因为它是基于事实数据的。

例如，在像*“法国的首都是什么？”*这样的问题中，RAG 系统会在其语料库中查找与法国及其首都巴黎相关的文档。它会检索相关段落，并生成类似于*“法国的首都是巴黎。”*的回答。这种方式非常适合简单查询和清晰记录的答案。

然而，RAG 在处理更复杂的查询时会遇到困难，特别是在需要理解实体之间的关系时，尤其当这些关系在检索的文档中没有明确表达时。系统在遇到像*“17世纪的科学贡献如何影响20世纪初的物理学？”*这样的提问时，可能会失败（*稍后会详细讨论这个例子*）。

**GraphRAG：利用知识图谱的力量**

GraphRAG，正如在微软研究博客中[这里](https://www.microsoft.com/en-us/research/blog/graphrag-unlocking-llm-discovery-on-narrative-private-data/)首次提出的那样，旨在通过将基于图的检索机制引入模型，克服这些限制。基本上，它将知识库的非结构化文本重新组织成结构化的知识图谱，其中节点代表实体（例如人、地点、概念），边代表实体之间的关系。这种结构化格式使得模型能够更好地理解和利用不同信息之间的相互关系。

![](../Images/eea3b90bdd345ebfb4c9af0a981020e6.png)

图片由[Alina Grubnyak](https://unsplash.com/@alinnnaaaa?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

现在让我们通过简单的方式来深入理解 GraphRAG 的概念，并与 RAG 进行比较。

首先，让我们假设有一个包含各种科学和历史文本的知识库，如下所示：

*1\. “阿尔伯特·爱因斯坦提出了相对论理论，彻底改变了理论物理学和天文学。”*

*2\. “相对论理论在20世纪初提出，深刻影响了我们对空间和时间的理解。”*

*3\. “艾萨克·牛顿以其运动定律和万有引力定律闻名，为经典力学奠定了基础。”*

*4\. “1915年，爱因斯坦提出了广义相对论，扩展了他早期的特殊相对论研究。”*

*5\. “牛顿在17世纪的工作为现代物理学奠定了基础。”*

在RAG系统中，这些句子将以非结构化文本的形式存储。例如，提问*“17世纪的科学贡献如何影响20世纪初的物理学？”*可能会使系统陷入困境，**如果文档的准确措辞和检索质量没有直接**将17世纪的影响与20世纪初的物理学联系起来，RAG可能会给出类似*“艾萨克·牛顿在17世纪的工作为现代物理学奠定了基础，阿尔伯特·爱因斯坦在20世纪初发展了相对论”*的回答，因为该机制能够检索到相关信息，但无法清晰解释17世纪物理学对20世纪初期发展的影响。

相比之下，GraphRAG将这段文字转化为结构化的知识图。知识图展示了不同事物之间的关系。它使用一组本体论，即一套帮助组织信息的规则。通过这种方式，它不仅能发现明显的联系，还能找到隐藏的联系。

使用GraphRAG系统，之前的知识库将被转化为如以下所示的节点和边。

```py
Nodes: Albert Einstein, theory of relativity, theoretical physics, astronomy, early 20th century, space, time, Isaac Newton, laws of motion, universal gravitation, classical mechanics, 1915, general theory of relativity, special relativity, 17th century, modern physics.
```

```py
Edges:
- (Albert Einstein) - [developed] → (theory of relativity)
- (theory of relativity) - [revolutionized] → (theoretical physics)
- (theory of relativity) - [revolutionized] → (astronomy)
- (theory of relativity) - [formulated in] → (early 20th century)
- (theory of relativity) - [impacted] → (understanding of space and time)
- (Isaac Newton) - [known for] → (laws of motion)
- (Isaac Newton) - [known for] → (universal gravitation)
- (Isaac Newton) - [laid the groundwork for] → (classical mechanics)
- (general theory of relativity) - [presented by] → (Albert Einstein)
- (general theory of relativity) - [expanded on] → (special relativity)
- (Newton's work) - [provided foundation for] → (modern physics)
```

当被提问*“17世纪的科学贡献如何影响20世纪初的物理学？”*时，基于GraphRAG的检索器能够识别从牛顿的工作到爱因斯坦的进展之间的联系，突出17世纪物理学对20世纪初期发展的影响。这种结构化的检索使得回答内容既富有背景信息又准确：*“艾萨克·牛顿在17世纪提出的运动定律和万有引力定律为经典力学奠定了基础。这些原则影响了阿尔伯特·爱因斯坦在20世纪初发展相对论的过程，后者扩展了我们对时空的理解。”*

GraphRAG中结构化知识图的使用增强了模型回答复杂问题的能力，同时通过为答案提供明确的关系基础，减少了幻觉的发生。这本质上赋予了GraphRAG在开发更可靠、智能的对话式问答系统方面的有效性。

将非结构化的知识库转化为结构化图谱，也使GraphRAG能够从信息中获得更深层次的意义，从而使语言模型能够在上下文中准确生成适当的回答。这是对话式AI向更先进、更可靠的聊天机器人系统发展的一个重要进步。

然而，正如GraphRAG的其他优点一样，也存在一些挑战。

首先，构建图谱是困难的。将一个无序的知识库转化为结构化的知识图非常具有挑战性。这需要复杂的方法来提取实体和识别关系，这可能会非常消耗计算资源。

其次，出现了可扩展性问题。知识图谱随着知识库的规模增长而变得更加复杂。如果图谱变得过大，以至于在运行时无法轻松遍历，这可能会引发可扩展性问题。一个主要挑战将是优化大规模图谱的图谱检索算法。

第三点谈到了维护开销：知识图谱需要不断地更新新信息以及现有数据的变化。在一些领域，这些领域可能经常发生变化，这可能成为一项非常昂贵的操作，尤其是在技术或医学领域。这意味着，尽管结果可能很有前景，但需要付出大量的努力来维护知识图谱的正确性和相关性。

然而，GraphRAG有望为未来的对话型AI代理带来更高的智能、可靠性和上下文意识。更多的研究和开发可以帮助缓解与GraphRAG相关的困难，为更复杂的AI驱动解决方案铺平道路。
