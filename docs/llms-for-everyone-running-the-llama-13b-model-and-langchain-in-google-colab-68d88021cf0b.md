# 每个人的 LLM：在 Google Colab 中运行 LLaMA-13B 模型和 LangChain

> 原文：[https://towardsdatascience.com/llms-for-everyone-running-the-llama-13b-model-and-langchain-in-google-colab-68d88021cf0b?source=collection_archive---------4-----------------------#2024-01-05](https://towardsdatascience.com/llms-for-everyone-running-the-llama-13b-model-and-langchain-in-google-colab-68d88021cf0b?source=collection_archive---------4-----------------------#2024-01-05)

## 免费实验大型语言模型（第二部分）

[](https://dmitryelj.medium.com/?source=post_page---byline--68d88021cf0b--------------------------------)[![Dmitrii Eliuseev](../Images/7c48f0c016930ead59ddb785eaf3e0e6.png)](https://dmitryelj.medium.com/?source=post_page---byline--68d88021cf0b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--68d88021cf0b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--68d88021cf0b--------------------------------) [Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--68d88021cf0b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--68d88021cf0b--------------------------------) ·14分钟阅读·2024年1月5日

--

![](../Images/a3ad498b55784156229ddc0304847682.png)

由 Glib Albovsky 拍摄，[Unsplash](https://unsplash.com/@albovsky)

在[故事的第一部分](/llms-for-everyone-running-langchain-and-a-mistralai-7b-model-in-google-colab-246ca94d7c4d)中，我们使用了一个免费的 Google Colab 实例来运行 Mistral-7B 模型，并使用 FAISS（Facebook AI 相似度搜索）数据库提取信息。在这一部分中，我们将进一步深入，我将展示如何运行 LLaMA 2 13B 模型；我们还将测试一些额外的 LangChain 功能，比如制作基于聊天的应用程序和使用代理。与第一部分相同，所有使用的组件都是基于开源项目，并且完全免费使用。

让我们开始吧！

## LLaMA.cpp

[LLaMA.CPP](https://github.com/ggerganov/llama.cpp) 是一个非常有趣的开源项目，最初设计用于在 MacBook 上运行 LLaMA 模型，但其功能远远超出了这一点。首先，它是用纯 C/C++ 编写的，没有外部依赖，能够在任何硬件上运行（支持 CUDA、OpenCL 和 Apple Silicon，甚至可以在 Raspberry Pi 上运行）。其次，LLaMA.CPP 可以与[LangChain](https://github.com/langchain-ai/langchain)连接，这使我们能够在没有 OpenAI 密钥的情况下免费测试其许多功能。最后但同样重要的是，由于 LLaMA.CPP 可以在任何地方运行，它是一个非常适合在免费的 Google Colab 实例中运行的候选者。提醒一下，Google 提供免费...
