# 如何构建一个 RAG 系统，以便轻松访问您的数据

> 原文：[https://towardsdatascience.com/how-to-make-a-rag-system-to-gain-powerful-access-to-your-data-caf4bb9186ea?source=collection_archive---------3-----------------------#2024-03-19](https://towardsdatascience.com/how-to-make-a-rag-system-to-gain-powerful-access-to-your-data-caf4bb9186ea?source=collection_archive---------3-----------------------#2024-03-19)

## 本文将展示如何构建一个 RAG 系统，使您的数据通过提示轻松访问。

[](https://oieivind.medium.com/?source=post_page---byline--caf4bb9186ea--------------------------------)[![Eivind Kjosbakken](../Images/5f91b74428e1202fc4a176a3dd1cb1c7.png)](https://oieivind.medium.com/?source=post_page---byline--caf4bb9186ea--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--caf4bb9186ea--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--caf4bb9186ea--------------------------------) [Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--caf4bb9186ea--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--caf4bb9186ea--------------------------------) ·阅读时间 13 分钟·2024年3月19日

--

RAG 系统是一种创新的信息检索方法。它结合了传统的信息检索方法，如向量相似度搜索，以及最先进的大型语言模型技术。这些技术的结合，构成了一个强大的系统，能够通过简单的提示访问大量信息。

![](../Images/c86151834b18ecded76f2e1f3ff81f8b.png)

ChatGPT 生成的 RAG 系统图像。图像来自 ChatGPT。“你能画一个 RAG 系统的插图，展示计算机如何访问知识库？”这个提示。*ChatGPT*，4，OpenAI，2024年3月17日。[https://chat.openai.com.](https://chat.openai.com.)

# 动机

我写这篇文章的动机来源于我在试图查找一封旧邮件时的沮丧。我通常会有一些关于邮件的信息，比如发件人是谁，或者大致知道邮件的主题是什么。然而，当我在 Gmail 中进行直接的关键词搜索时，我必须更加具体，这使得找到我想要的那封邮件变得具有挑战性。我希望能有一个 RAG 系统，允许我通过提示邮件进行搜索。举个例子，如果我需要一封来自我大学的旧邮件，内容是关于某个科目的，我可以像这样发出提示：“我在 NTNU 第二年时修读了什么技术课程？”。与这个提示等效的直接关键词搜索具有挑战性，因为我在提示中需要更多具体的信息。相反，如果有一个 RAG 系统，它可以根据邮件中已有的内容找到这封邮件……
