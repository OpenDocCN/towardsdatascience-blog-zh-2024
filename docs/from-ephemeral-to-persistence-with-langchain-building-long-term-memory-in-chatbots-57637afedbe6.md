# 从短暂到持久：使用 LangChain 在聊天机器人中构建长期记忆

> 原文：[`towardsdatascience.com/from-ephemeral-to-persistence-with-langchain-building-long-term-memory-in-chatbots-57637afedbe6?source=collection_archive---------9-----------------------#2024-07-23`](https://towardsdatascience.com/from-ephemeral-to-persistence-with-langchain-building-long-term-memory-in-chatbots-57637afedbe6?source=collection_archive---------9-----------------------#2024-07-23)

## 详细介绍如何将简单的聊天机器人转变为具有长期记忆和上下文理解的复杂 AI 助手

[](https://medium.com/@menghani.deepsha?source=post_page---byline--57637afedbe6--------------------------------)![Deepsha Menghani](https://medium.com/@menghani.deepsha?source=post_page---byline--57637afedbe6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--57637afedbe6--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--57637afedbe6--------------------------------) [Deepsha Menghani](https://medium.com/@menghani.deepsha?source=post_page---byline--57637afedbe6--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--57637afedbe6--------------------------------) ·8 分钟阅读·2024 年 7 月 23 日

--

![](img/5962e958170230a845d14257b30ac5de.png)

使用 BingAI 生成

在我之前的一篇[文章](https://medium.com/data-science-at-microsoft/how-to-build-a-fine-tuned-customer-service-chatbot-with-python-and-openai-88e221e5bf36)中，我介绍了如何用 OpenAI 创建一个对话型聊天机器人。然而，如果你使用过类似 ChatGPT 或 Claude 等聊天机器人界面，你会注意到，当会话关闭并重新打开时，记忆会被保留，你可以从中断的地方继续对话。这正是我想在本文中创建的体验。

我将以 LangChain 作为基础，它提供了非常棒的工具来管理对话历史，并且如果你希望通过构建链来发展更复杂的应用程序，它也非常适合。

# 代码可用性

本文中重建所有内容的代码可以在[`github.com/deepshamenghani/langchain_openai_persistence`](https://github.com/deepshamenghani/langchain_openai_persistence)找到。

# 使用 LangChain 和 OpenAI 构建的单一问答机器人

我将首先创建一个循环，允许用户为聊天机器人输入问题。我会将这个输入分配给变量`humaninput`。现在，暂时不使用 LLM 的输出……
