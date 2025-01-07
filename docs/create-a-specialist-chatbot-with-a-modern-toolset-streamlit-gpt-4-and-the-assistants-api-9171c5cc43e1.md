# 如何使用 OpenAI 的助手 API 和 Streamlit 创建专业聊天机器人

> 原文：[`towardsdatascience.com/create-a-specialist-chatbot-with-a-modern-toolset-streamlit-gpt-4-and-the-assistants-api-9171c5cc43e1?source=collection_archive---------11-----------------------#2024-01-30`](https://towardsdatascience.com/create-a-specialist-chatbot-with-a-modern-toolset-streamlit-gpt-4-and-the-assistants-api-9171c5cc43e1?source=collection_archive---------11-----------------------#2024-01-30)

## 借助 Streamlit 的精密聊天界面、强大的 GPT-4 后端和 OpenAI 助手 API，我们几乎可以构建任何专业的聊天机器人

[](https://medium.com/@alan-jones?source=post_page---byline--9171c5cc43e1--------------------------------)![Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--9171c5cc43e1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9171c5cc43e1--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9171c5cc43e1--------------------------------) [Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--9171c5cc43e1--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9171c5cc43e1--------------------------------) ·10 分钟阅读·2024 年 1 月 30 日

--

![](img/9f3303b2dd938245ce59bbb72f3d7377.png)

一位工艺大师可以用这些工具创造美丽的东西。我们能否用 AI 做同样的事？图片来源：[Ricky Kharawala](https://unsplash.com/@sweetmangostudios?utm_source=medium&utm_medium=referral) 通过 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

*请注意，助手 API 是一个 Beta 版本，可能会发生变化。自本文编写以来，OpenAI 已发布了多个版本的库，它们不一定与本文中的代码兼容，本文代码是基于 OpenAI 版本 1.6.1 编写的。为了确保兼容性，请使用此版本。*

使用正确的工具——Streamlit、GPT-4 LLM 和助手 API——我们几乎可以构建任何聊天机器人。

+   **Streamlit**结合了 Python 编程的强大功能和易于构建的聊天界面，为基于 AI 的应用程序提供了理想的前端；

+   **GPT-4**是迄今为止 OpenAI 最强大的 LLM；

+   **助手 API**提供了二者之间的连接，控制 LLM 的行为，组织前端和后端之间的通信，并提供访问工具，如代码解释和数据检索。
