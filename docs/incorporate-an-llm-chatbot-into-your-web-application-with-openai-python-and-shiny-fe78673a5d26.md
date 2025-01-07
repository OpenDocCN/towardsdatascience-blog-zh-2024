# 将 LLM 聊天机器人集成到您的网页应用程序中，使用 OpenAI、Python 和 Shiny

> 原文：[https://towardsdatascience.com/incorporate-an-llm-chatbot-into-your-web-application-with-openai-python-and-shiny-fe78673a5d26?source=collection_archive---------13-----------------------#2024-06-18](https://towardsdatascience.com/incorporate-an-llm-chatbot-into-your-web-application-with-openai-python-and-shiny-fe78673a5d26?source=collection_archive---------13-----------------------#2024-06-18)

## 将 AI 聊天机器人一步步集成到 Shiny for Python 应用程序中：从 API 设置到用户互动

[](https://medium.com/@menghani.deepsha?source=post_page---byline--fe78673a5d26--------------------------------)[![Deepsha Menghani](../Images/56a6ed8597c36e8c76d8a29a449325a4.png)](https://medium.com/@menghani.deepsha?source=post_page---byline--fe78673a5d26--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fe78673a5d26--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fe78673a5d26--------------------------------) [Deepsha Menghani](https://medium.com/@menghani.deepsha?source=post_page---byline--fe78673a5d26--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fe78673a5d26--------------------------------) ·阅读时间：8分钟·2024年6月18日

--

![](../Images/7285be0ede88d7ab8f66ec5ea5c19a6c.png)

由[BoliviaInteligente](https://unsplash.com/@boliviainteligente?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，照片来源于[Unsplash](https://unsplash.com/photos/a-close-up-of-a-keyboard-with-a-blue-button-DEci5GH0r0k?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

几天后，我将在即将召开的[卡斯卡迪亚数据科学会议](https://cascadiarconf.com/)上发表关于生成性人工智能的主题演讲。为了这次演讲，我想为会议定制一些内容，因此我创建了一个能够回答关于会议议程问题的聊天机器人。为了展示这个功能，我通过 Shiny for Python 网络应用程序提供了这个聊天机器人。[Shiny](https://shiny.posit.co/)是一个框架，可以用来创建可以在后台运行代码的互动式网页应用程序。

除了原型开发，Shiny 中提供聊天机器人的一个重要应用场景是回答关于仪表板中各个领域的文档问题。例如，如果仪表板用户想了解图表中客户流失指标是如何创建的，Shiny 应用程序中的聊天机器人可以让用户用自然语言提问，并直接获得答案，而无需翻阅大量文档。

在本文中，我将介绍如何使用 OpenAI 在 Shiny for Python 网络应用程序中集成和服务聊天机器人。让我们开始吧！
