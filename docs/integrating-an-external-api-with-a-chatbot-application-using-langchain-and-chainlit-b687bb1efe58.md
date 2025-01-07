# 使用 LangChain 和 Chainlit 集成外部 API 与聊天机器人应用程序

> 原文：[https://towardsdatascience.com/integrating-an-external-api-with-a-chatbot-application-using-langchain-and-chainlit-b687bb1efe58?source=collection_archive---------1-----------------------#2024-02-18](https://towardsdatascience.com/integrating-an-external-api-with-a-chatbot-application-using-langchain-and-chainlit-b687bb1efe58?source=collection_archive---------1-----------------------#2024-02-18)

## 使用 LangChain 和 Chainlit 集成外部 API 以实现与聊天机器人应用程序的高级交互的实用指南

[](https://medium.com/@tahreemrasul?source=post_page---byline--b687bb1efe58--------------------------------)[![Tahreem Rasul](../Images/fbec150343ddb89ee07168e261595f8e.png)](https://medium.com/@tahreemrasul?source=post_page---byline--b687bb1efe58--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b687bb1efe58--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b687bb1efe58--------------------------------) [Tahreem Rasul](https://medium.com/@tahreemrasul?source=post_page---byline--b687bb1efe58--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b687bb1efe58--------------------------------) ·11 分钟阅读·2024年2月18日

--

在本教程中，我们将展示如何将外部 API 与自定义聊天机器人应用程序集成。在我之前关于构建自定义聊天机器人应用程序的文章中，我们介绍了如何使用[LangChain](https://www.langchain.com)和[OpenAI](https://openai.com)创建具有特定功能的聊天机器人，以及如何使用[Chainlit](https://docs.chainlit.io/get-started/overview)为我们的聊天机器人构建 Web 应用程序。

![](../Images/5efd44b6137ff58709774f2d6500dbdf.png)

API 集成聊天机器人的工作流程 — 作者提供的图像

如果你是这个系列的新读者，我建议查看我之前的文章，以获取详细的逐步指南：

[](https://medium.com/@tahreemrasul/building-a-chatbot-application-with-chainlit-and-langchain-3e86da0099a6?source=post_page-----b687bb1efe58--------------------------------) [## 使用 Chainlit 和 LangChain 构建聊天机器人应用程序

### 在本文中，我们将使用 Chainlit 框架为我们的自定义聊天机器人 Scoopsie 开发应用程序界面…

medium.com](https://medium.com/@tahreemrasul/building-a-chatbot-application-with-chainlit-and-langchain-3e86da0099a6?source=post_page-----b687bb1efe58--------------------------------) [](https://medium.com/@tahreemrasul/how-to-build-your-own-chatbot-with-langchain-and-openai-f092822b6ba6?source=post_page-----b687bb1efe58--------------------------------) [## 如何使用 LangChain 和 OpenAI 构建自己的聊天机器人

### 在本教程中，我们将使用LangChain和OpenAI构建一个针对特定用例的聊天机器人。如果你…

medium.com](https://medium.com/@tahreemrasul/how-to-build-your-own-chatbot-with-langchain-and-openai-f092822b6ba6?source=post_page-----b687bb1efe58--------------------------------)
