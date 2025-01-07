# 如何构建一个本地开源的LLM聊天机器人，结合RAG技术

> 原文：[https://towardsdatascience.com/how-to-build-a-local-open-source-llm-chatbot-with-rag-f01f73e2a131?source=collection_archive---------1-----------------------#2024-03-31](https://towardsdatascience.com/how-to-build-a-local-open-source-llm-chatbot-with-rag-f01f73e2a131?source=collection_archive---------1-----------------------#2024-03-31)

## 与谷歌的Gemma-2b-it、LangChain和Streamlit一起与PDF文档对话

[](https://medium.com/@leoneversberg?source=post_page---byline--f01f73e2a131--------------------------------)[![Dr. Leon Eversberg](../Images/56dc3579a29933f7047a9ce60be4697a.png)](https://medium.com/@leoneversberg?source=post_page---byline--f01f73e2a131--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f01f73e2a131--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f01f73e2a131--------------------------------) [Dr. Leon Eversberg](https://medium.com/@leoneversberg?source=post_page---byline--f01f73e2a131--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f01f73e2a131--------------------------------) ·阅读时间12分钟·2024年3月31日

--

![](../Images/be316330826a614eb9201a18879fcfd1.png)

本文中我们将构建一个结合RAG的LLM聊天机器人，它使用洗衣机用户手册回答具体问题。图片由作者提供

# 介绍

大型语言模型（LLM）在将世界知识压缩到它们数十亿个参数中非常出色。

然而，LLM有两个主要的局限性：它们只能获取到上次训练迭代时的最新知识。而且在被问及具体问题时，它们有时会“编造”知识（出现幻觉）。

使用RAG技术，我们可以让预训练的LLM在回答问题时，访问非常具体的信息作为额外的上下文。

在这篇文章中，我将通过理论与实践，演示如何利用Hugging Face的transformers库、LangChain以及Faiss向量数据库，实现谷歌的LLM Gemma，并增加RAG功能。

下图展示了RAG管道的概述，我们将一步一步地实现它。

![](../Images/bd04d3634055266566928f252271a642.png)

RAG管道实现概述。图片由作者提供

# 检索增强生成（RAG）
