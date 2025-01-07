# 如何为Gmail创建强大的AI电子邮件搜索系统（使用RAG）

> 原文：[https://towardsdatascience.com/how-to-create-a-powerful-ai-email-search-for-gmail-with-rag-88d2bdb1aedc?source=collection_archive---------8-----------------------#2024-09-10](https://towardsdatascience.com/how-to-create-a-powerful-ai-email-search-for-gmail-with-rag-88d2bdb1aedc?source=collection_archive---------8-----------------------#2024-09-10)

## 学习如何开发一个应用来使用RAG搜索电子邮件

[](https://oieivind.medium.com/?source=post_page---byline--88d2bdb1aedc--------------------------------)[![Eivind Kjosbakken](../Images/5f91b74428e1202fc4a176a3dd1cb1c7.png)](https://oieivind.medium.com/?source=post_page---byline--88d2bdb1aedc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--88d2bdb1aedc--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--88d2bdb1aedc--------------------------------) [Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--88d2bdb1aedc--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--88d2bdb1aedc--------------------------------) ·13分钟阅读·2024年9月10日

--

在本文中，我将向你展示如何开发[MailDiscoverer](https://maildiscoverer.streamlit.app/)应用，以使用RAG搜索Gmail电子邮件。首先，我将展示如何设置身份验证管道来访问用户的电子邮件（如果已获得同意）。然后，使用OpenAI的文本嵌入器将电子邮件嵌入，并将其存储在Pinecone向量数据库中。这允许用户就电子邮件提出问题，RAG系统将检索最相关的电子邮件并提供问题的答案。

![](../Images/4fdc5aad4c64e2265d43a0acadecd413.png)

学习如何开发一个RAG系统来搜索你的电子邮件。图片来自ChatGPT。OpenAI。（2024年）。*ChatGPT*（4.0）[大型语言模型]。[https://chatgpt.com/c/66dd8280-5bc4-8012-9e7e-68fd66ccbfeb](https://chatgpt.com/c/66dd8280-5bc4-8012-9e7e-68fd66ccbfeb)

本文中开发的应用可以在[Streamlit上找到](https://maildiscoverer.streamlit.app/)。我在GitHub上的代码库[也可以访问](https://github.com/EivindKjosbakken/maildiscoverer)。

下面的视频展示了该应用如何工作。在登录并上传电子邮件后，你可以提出问题，应用将提供答案，并展示用于提供答案的最相关电子邮件。

# 动机
