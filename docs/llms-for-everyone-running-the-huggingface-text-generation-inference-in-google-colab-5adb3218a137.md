# LLMs 为每个人：在 Google Colab 上运行 HuggingFace 文本生成推理

> 原文：[`towardsdatascience.com/llms-for-everyone-running-the-huggingface-text-generation-inference-in-google-colab-5adb3218a137?source=collection_archive---------3-----------------------#2024-01-13`](https://towardsdatascience.com/llms-for-everyone-running-the-huggingface-text-generation-inference-in-google-colab-5adb3218a137?source=collection_archive---------3-----------------------#2024-01-13)

## 免费试验大语言模型（第三部分）

[](https://dmitryelj.medium.com/?source=post_page---byline--5adb3218a137--------------------------------)![Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--5adb3218a137--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5adb3218a137--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5adb3218a137--------------------------------) [Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--5adb3218a137--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5adb3218a137--------------------------------) ·阅读时长 7 分钟·2024 年 1 月 13 日

--

![](img/c7f6ab5d005b7e9089c8aa882f7f43e4.png)

图片来源：Markus Spiske，[Unsplash](https://unsplash.com/@markusspiske)

在第一部分中，我们使用免费的 Google Colab 实例运行了 Mistral-7B 模型，并通过 FAISS（Facebook AI 相似度搜索）数据库提取信息。在第二部分中，我们使用了 LLaMA-13B 模型和 LangChain 库进行聊天，并实现了文本摘要及其他功能。在本部分中，我将展示如何使用 HuggingFace 🤗 [文本生成推理](https://huggingface.co/docs/text-generation-inference/index)（TGI）。TGI 是一个工具包，允许我们将大语言模型（LLM）作为服务运行。与之前的部分一样，我们将在 Google Colab 实例中测试它，完全免费。

## 文本生成推理

文本生成推理（TGI）是一个适用于大语言模型（LLMs）的生产级工具包，用于部署和提供服务。将 LLM 作为服务运行，允许我们在不同的客户端之间使用它，从 Python 笔记本到移动应用程序。测试 TGI 的功能非常有趣，但事实证明，它的系统要求相当高，并且并非一切都如预期般顺利：

+   免费的 Google Colab 实例仅提供 12.7 GB 的 RAM，这通常不足以一次性加载 13B 甚至 7B 模型……
