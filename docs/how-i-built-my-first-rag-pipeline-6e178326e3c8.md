# 如何构建我的第一个 RAG 流水线

> 原文：[`towardsdatascience.com/how-i-built-my-first-rag-pipeline-6e178326e3c8?source=collection_archive---------4-----------------------#2024-08-02`](https://towardsdatascience.com/how-i-built-my-first-rag-pipeline-6e178326e3c8?source=collection_archive---------4-----------------------#2024-08-02)

## 一个 RAG 流水线来回答你所有招聘人员的问题！

[](https://datascidiana.medium.com/?source=post_page---byline--6e178326e3c8--------------------------------)![Diana Morales](https://datascidiana.medium.com/?source=post_page---byline--6e178326e3c8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6e178326e3c8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6e178326e3c8--------------------------------) [Diana Morales](https://datascidiana.medium.com/?source=post_page---byline--6e178326e3c8--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6e178326e3c8--------------------------------) ·阅读时长 5 分钟·2024 年 8 月 2 日

--

*大型语言模型的幻觉问题即使对于像 Google 这样的科技巨头也一直存在（只需问 Gemini 每天推荐吃多少块岩石… 剧透警告，答案是每天一块）。虽然我们仍然不知道如何教大型语言模型常识，但我们可以做的是为其提供足够的上下文，针对你的具体应用场景。这就是检索增强生成（RAG）派上用场的地方！在这篇文章中，我将带你了解* ***我是如何实现一个 RAG 流水线，让它可以读取我的简历并为我与招聘人员对话的！***

> 嘘！如果你没有会员资格，你可以在[这里](https://datascidiana.medium.com/how-i-built-my-first-rag-pipeline-6e178326e3c8?sk=52047168e39299dbd026bb036b32861e)阅读这篇文章。

# 什么是检索增强生成（RAG）？

首先，让我们打好基础，确保理解什么是 RAG 以及它是如何工作的。简而言之，检索增强生成（RAG）是一种技术，其中大型语言模型（LLM）生成答案时，*通过检索*从知识库中获得的相关信息来*增强*其回答。RAG 流水线从你的私有数据中挑选出最相关的文本片段，让大型语言模型与提示一起阅读并*生成*答案。例如，在这篇文章中，我正在构建一个简单的聊天机器人，它可以回答招聘人员的问题。为了让大型语言模型准确地完成任务，我必须“告诉”它我是谁。通过使用 RAG 流水线，我可以让它*检索*我简历中最相关的部分，以回答每一个招聘人员的问题，从而*增强*大型语言模型的… 
