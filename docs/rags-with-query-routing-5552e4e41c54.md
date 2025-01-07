# 如何通过查询路由构建有用的 RAG（检索增强生成）。

> 原文：[`towardsdatascience.com/rags-with-query-routing-5552e4e41c54?source=collection_archive---------0-----------------------#2024-08-16`](https://towardsdatascience.com/rags-with-query-routing-5552e4e41c54?source=collection_archive---------0-----------------------#2024-08-16)

## 一个大型语言模型（LLM）可以处理一般的路由问题。语义搜索则能更好地处理私有数据。你会选择哪一个？

[](https://thuwarakesh.medium.com/?source=post_page---byline--5552e4e41c54--------------------------------)![Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--5552e4e41c54--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5552e4e41c54--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5552e4e41c54--------------------------------) [Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--5552e4e41c54--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5552e4e41c54--------------------------------) ·阅读时长 10 分钟·2024 年 8 月 16 日

--

![](img/286570f75241cabf27353a4228d817d7.png)

图片来源：[Monstera Production](https://www.pexels.com/photo/wind-rose-with-maps-on-table-7412085/)

一个单一的提示无法处理所有问题，而单一的数据源也可能无法适应所有数据。

这是你在生产环境中经常看到的，但在演示中却不常见的情况：

> 你需要多个数据源来检索信息。可能需要多个向量存储、图数据库，甚至是 SQL 数据库。而且你还需要不同的提示来处理不同的任务。

如果是这样，我们就有一个问题。面对结构化差、通常含糊不清且格式不规范的用户输入，我们如何决定从哪个数据库中检索数据？

如果出于某种原因，你仍然觉得这太简单了，下面是一个例子。

假设你有一个导游聊天机器人，一个旅客请求获取五个地点之间的最优旅行计划。让 LLM 来回答可能会出现幻觉，因为 LLM 在基于位置的计算方面并不擅长。

相反，如果你将这些信息存储在图数据库中，LLM 可能会生成一个查询来获取各点之间的最短旅行路径…
