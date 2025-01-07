# 如何使用 iPhone 与任何开源 LLM 免费聊天

> 原文：[https://towardsdatascience.com/how-to-chat-with-any-open-source-llm-for-free-with-your-iphone-5fa7549efa9a?source=collection_archive---------8-----------------------#2024-02-05](https://towardsdatascience.com/how-to-chat-with-any-open-source-llm-for-free-with-your-iphone-5fa7549efa9a?source=collection_archive---------8-----------------------#2024-02-05)

## 使用 Ollama 和 Google Colab 免费 T4 GPU 在 iPhone 上构建开源“ChatGPT”应用

[](https://medium.com/@yanli.liu?source=post_page---byline--5fa7549efa9a--------------------------------)[![Yanli Liu](../Images/31342655ab635eb38e3ce501235f1b89.png)](https://medium.com/@yanli.liu?source=post_page---byline--5fa7549efa9a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5fa7549efa9a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5fa7549efa9a--------------------------------) [Yanli Liu](https://medium.com/@yanli.liu?source=post_page---byline--5fa7549efa9a--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5fa7549efa9a--------------------------------) ·阅读时间：6 分钟·2024年2月5日

--

![](../Images/ab9e0c9fccde9a832ffd2ed22d756102.png)

开源“ChatGPT” UI 演示。图像由作者提供。

厌倦了支付高昂的订阅费用，或不愿与 OpenAI 分享个人数据？

如果有免费的、更安全的替代方案，使用功能强大的开源模型呢？

如果你感兴趣，那么这篇指南就是为你准备的。让我们一起在 iPhone 上构建自己的“ChatGPT”，由最强大的开源模型驱动！

在后端，我们将利用**Ollama**和 Google Colab 的**免费 T4 GPU**来服务 LLMs。前端我们将使用**Enchanted**，一款优雅的开源 iOS 应用，与 Llama 2、Mistral、Phi-2 等模型进行交互。

在本指南结束时，你将拥有一个强大的 AI 随时掌握——而且完全免费。最棒的是？你可以根据需求轻松切换最佳的开源模型！

准备好了吗？让我们开始吧！

# 关键组件的快速概述

要构建我们的开源“ChatGPT”，我们将使用以下关键组件：

+   **Google Colab 笔记本**

+   **Ollama**：一款开源工具…
