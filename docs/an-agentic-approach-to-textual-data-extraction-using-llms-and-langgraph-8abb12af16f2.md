# 使用 LLMs 和 LangGraph 进行文本数据提取的代理方法

> 原文：[`towardsdatascience.com/an-agentic-approach-to-textual-data-extraction-using-llms-and-langgraph-8abb12af16f2?source=collection_archive---------4-----------------------#2024-10-29`](https://towardsdatascience.com/an-agentic-approach-to-textual-data-extraction-using-llms-and-langgraph-8abb12af16f2?source=collection_archive---------4-----------------------#2024-10-29)

## 从非结构化的维基百科文本到结构化的 JSON：一步步指南。

[](https://medium.com/@CVxTz?source=post_page---byline--8abb12af16f2--------------------------------)![Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--8abb12af16f2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8abb12af16f2--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8abb12af16f2--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--8abb12af16f2--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8abb12af16f2--------------------------------) ·阅读时间：8 分钟·2024 年 10 月 29 日

--

![](img/11690239db43ef7ceaf20920ca662bd6.png)

图片由[Mesut Kaya](https://unsplash.com/@directormesut?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

假设你被要求构建一个包含全球所有城市和旅游地点的清晰且结构化的数据集。这个数据集可以用来为用户提供旅行建议或规划旅行。你知道这些信息存在于维基百科中，并且由于社区的努力，数据会不断更新，但它们都以纯文本或半结构化的方式呈现，并不符合你的需求。

这种类型的问题，即缺失符合特定格式的高质量数据，是数据科学家和机器学习工程师在解决商业问题时面临的主要挑战之一。本文尝试将大规模的文本语料库转化为可用、相关且结构化的数据。

我们将探讨的解决方案的基本思路是利用 LLMs 的零样本能力来构建一个文本到结构化数据集的管道。虽然有多种方法可以实现这一目标，但我们将探索基于代理的方法来进行类似 JSON 的结构化信息解析。这涉及到将包含多个、可能相互关联的实体类型的复杂信息解析为结构化格式。
