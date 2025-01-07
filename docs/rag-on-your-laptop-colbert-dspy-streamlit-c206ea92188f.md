# 在你的笔记本电脑上构建并运行你自己的 RAG：ColBERT + DSPy + Streamlit

> 原文：[https://towardsdatascience.com/rag-on-your-laptop-colbert-dspy-streamlit-c206ea92188f?source=collection_archive---------2-----------------------#2024-03-13](https://towardsdatascience.com/rag-on-your-laptop-colbert-dspy-streamlit-c206ea92188f?source=collection_archive---------2-----------------------#2024-03-13)

![](../Images/250fcc511d345830f2fa9e220b017cc2.png)

照片由 [Tamara Gak](https://unsplash.com/@tamara_photography?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## *生成型 AI 初学者教程：让我们一步步在本地构建一个非常简单的 RAG（检索增强生成）系统*

[](https://medium.com/@ngshya?source=post_page---byline--c206ea92188f--------------------------------)[![Shuyi Yang](../Images/73640b84246487db835da320b2592f9e.png)](https://medium.com/@ngshya?source=post_page---byline--c206ea92188f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c206ea92188f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c206ea92188f--------------------------------) [Shuyi Yang](https://medium.com/@ngshya?source=post_page---byline--c206ea92188f--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c206ea92188f--------------------------------) ·阅读时间：16 分钟·2024年3月13日

--

**检索增强生成（Retrieval-Augmented Generation，RAG）**系统就像一个智能助手，帮助你完成任务。假设你正在写关于某个主题的文章。你脑海里有一些知识（就像生成型 AI 一样），但是你可能记不住所有的细节。所以，你会查找书籍或互联网中的信息（这就是“检索”部分）。一旦找到相关信息，你会用自己的话来帮助撰写报告（这就是“生成”部分）。因此，RAG 系统是一个结合了自身知识（就像你的记忆）和查找其他信息源的能力（就像你的书籍或笔记）的工具，用来生成（写作）回应或完成任务。

通常，RAG 系统的复杂性和要求需要使用强大的计算机或云服务。在本教程中，我们将一步步在本地构建一个非常简单的检索增强生成系统（即在运行阶段不使用在线服务），并使用最先进的框架或工具。这里概述的过程非常基础，与已建立的做法有显著差异……
