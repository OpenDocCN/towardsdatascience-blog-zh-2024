# 构建个人 AI 助手：逐步指南，打造文本与语音本地大语言模型

> 原文：[`towardsdatascience.com/building-your-own-personal-ai-assistant-a-step-by-step-guide-to-text-and-voice-interaction-with-a-07389c5fd874?source=collection_archive---------0-----------------------#2024-03-14`](https://towardsdatascience.com/building-your-own-personal-ai-assistant-a-step-by-step-guide-to-text-and-voice-interaction-with-a-07389c5fd874?source=collection_archive---------0-----------------------#2024-03-14)

## 如何创建一个可以进行语音交互的本地大语言模型 AI 助手

[](https://medium.com/@amirarsalan.rajabi?source=post_page---byline--07389c5fd874--------------------------------)![Amirarsalan Rajabi](https://medium.com/@amirarsalan.rajabi?source=post_page---byline--07389c5fd874--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--07389c5fd874--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--07389c5fd874--------------------------------) [Amirarsalan Rajabi](https://medium.com/@amirarsalan.rajabi?source=post_page---byline--07389c5fd874--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--07389c5fd874--------------------------------) ·阅读时间 8 分钟·2024 年 3 月 14 日

--

![](img/a9528c05f159035c8106c93e01d79888.png)

由 DALL·E 3 生成的图片

在本教程中，我们将创建一个个人的本地大语言模型助手，你可以与其进行对话。你将能够使用麦克风录制你的声音并发送给大语言模型。大语言模型将以文本和语音的方式返回答案。

下面是应用程序的工作原理：

你可以在这个 GitHub 仓库中找到代码：

[`github.com/amirarsalan90/personal_llm_assistant`](https://github.com/amirarsalan90/personal_llm_assistant)

应用程序的主要组件包括：

+   本地大语言模型（由[llama-cpp-python](https://github.com/abetlen/llama-cpp-python)托管）

+   语音转文本（[Whisper](http://openai/whisper-large-v2)）

+   文本转语音（[Bark](http://suno/bark)）

# llama-cpp-python

Llama-cpp-python 是一个 Python 绑定库，用于连接伟大的 llama.cpp，它在 C/C++中实现了许多大语言模型。由于其广泛的应用……
