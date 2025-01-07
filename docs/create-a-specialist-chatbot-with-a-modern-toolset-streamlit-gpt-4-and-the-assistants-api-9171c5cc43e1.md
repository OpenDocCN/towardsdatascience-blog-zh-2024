# 如何使用OpenAI的助手API和Streamlit创建专业聊天机器人

> 原文：[https://towardsdatascience.com/create-a-specialist-chatbot-with-a-modern-toolset-streamlit-gpt-4-and-the-assistants-api-9171c5cc43e1?source=collection_archive---------11-----------------------#2024-01-30](https://towardsdatascience.com/create-a-specialist-chatbot-with-a-modern-toolset-streamlit-gpt-4-and-the-assistants-api-9171c5cc43e1?source=collection_archive---------11-----------------------#2024-01-30)

## 借助Streamlit的精密聊天界面、强大的GPT-4后端和OpenAI助手API，我们几乎可以构建任何专业的聊天机器人

[](https://medium.com/@alan-jones?source=post_page---byline--9171c5cc43e1--------------------------------)[![Alan Jones](../Images/359379fab1d6685ff08080b98173e67c.png)](https://medium.com/@alan-jones?source=post_page---byline--9171c5cc43e1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9171c5cc43e1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9171c5cc43e1--------------------------------) [Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--9171c5cc43e1--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9171c5cc43e1--------------------------------) ·10分钟阅读·2024年1月30日

--

![](../Images/9f3303b2dd938245ce59bbb72f3d7377.png)

一位工艺大师可以用这些工具创造美丽的东西。我们能否用AI做同样的事？图片来源：[Ricky Kharawala](https://unsplash.com/@sweetmangostudios?utm_source=medium&utm_medium=referral) 通过 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

*请注意，助手API是一个Beta版本，可能会发生变化。自本文编写以来，OpenAI已发布了多个版本的库，它们不一定与本文中的代码兼容，本文代码是基于OpenAI版本1.6.1编写的。为了确保兼容性，请使用此版本。*

使用正确的工具——Streamlit、GPT-4 LLM和助手API——我们几乎可以构建任何聊天机器人。

+   **Streamlit**结合了Python编程的强大功能和易于构建的聊天界面，为基于AI的应用程序提供了理想的前端；

+   **GPT-4**是迄今为止OpenAI最强大的LLM；

+   **助手API**提供了二者之间的连接，控制LLM的行为，组织前端和后端之间的通信，并提供访问工具，如代码解释和数据检索。
