# 如何使用 OpenAI + Python 构建 AI 助手

> 原文：[`towardsdatascience.com/how-to-build-an-ai-assistant-with-openai-python-8b3b5a636f69?source=collection_archive---------0-----------------------#2024-02-08`](https://towardsdatascience.com/how-to-build-an-ai-assistant-with-openai-python-8b3b5a636f69?source=collection_archive---------0-----------------------#2024-02-08)

## 使用 Assistants API 和 Fine-tuning 的逐步指南

[](https://shawhin.medium.com/?source=post_page---byline--8b3b5a636f69--------------------------------)![Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--8b3b5a636f69--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8b3b5a636f69--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8b3b5a636f69--------------------------------) [Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--8b3b5a636f69--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8b3b5a636f69--------------------------------) ·阅读时间 13 分钟·2024 年 2 月 8 日

--

![](img/422822b7ef30d18357a84c8fbb3a2ca5.png)

图片来自 Canva。

我从客户那里收到的最常见问题之一是：“*我如何用我的数据创建一个自定义聊天机器人？*” 虽然六个月前，这可能需要几个月的开发时间，但今天情况不一定是这样。在本文中，我将提供一个逐步指南，介绍如何使用 OpenAI 的 Assistants 和 Fine-tuning API 创建一个自定义 AI。每种方法都提供了 Python [示例代码](https://github.com/ShawhinT/YouTube-Blog/tree/main/LLMs/ai-assistant-openai)。

# **聊天机器人与助手**

在深入示例代码之前，我想简要区分 AI 聊天机器人和助手。虽然这些术语经常被交替使用，但在这里，我将它们表示为不同的概念。

**聊天机器人**是**一种可以与之对话的 AI**，而**AI 助手**是**一种可以使用工具的聊天机器人**。工具可以是网页浏览器、计算器、Python 解释器或任何其他扩展聊天机器人功能的东西 [1]。

例如，如果你使用的是免费版的 ChatGPT，那就是一个聊天机器人，因为它只有基本的聊天功能。然而，如果你使用的是 ChatGPT 的付费版，那就是一个助手，因为它具备网页浏览、知识检索和图像生成等功能。
