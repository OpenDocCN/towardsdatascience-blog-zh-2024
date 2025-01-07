# 使用LLM和神经网络在你的CPU笔记本上构建本地语音助手

> 原文：[https://towardsdatascience.com/building-a-local-voice-assistant-with-llms-and-neural-networks-on-your-cpu-laptop-95a876c11130?source=collection_archive---------6-----------------------#2024-11-19](https://towardsdatascience.com/building-a-local-voice-assistant-with-llms-and-neural-networks-on-your-cpu-laptop-95a876c11130?source=collection_archive---------6-----------------------#2024-11-19)

## 使用Python运行轻量级LLM的实用指南

[](https://medium.com/@yuchengtsai84?source=post_page---byline--95a876c11130--------------------------------)[![Yu-Cheng Tsai](../Images/c0ec2d4b9fea512040c8e6e0250670fc.png)](https://medium.com/@yuchengtsai84?source=post_page---byline--95a876c11130--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--95a876c11130--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--95a876c11130--------------------------------) [Yu-Cheng Tsai](https://medium.com/@yuchengtsai84?source=post_page---byline--95a876c11130--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--95a876c11130--------------------------------) ·6分钟阅读·2024年11月19日

--

![](../Images/17dce9bca09f4329f2d734c7d9269b72.png)

图片来自[Jacek Dylag](https://unsplash.com/@dylu?utm_source=medium&utm_medium=referral)，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

请享受阅读：免费[链接](/building-a-local-voice-assistant-with-llms-and-neural-networks-on-your-cpu-laptop-95a876c11130?sk=f88e589158de878fea44d1e9420944f0)!

随着多模态大型语言模型（LLM）的崛起，我们现在可以通过多种方式与它们互动，不仅限于输入文本，还可以使用音频输入。OpenAI最近为ChatGPT推出了语音功能，允许用户直接与聊天平台进行对话。这为围绕这一功能构建各种新颖的应用和机会开辟了无数可能性。

作为机器学习和数据科学的从业者，现在是一个令人兴奋的时刻。使用OpenAI的[实时语音转语音API](https://platform.openai.com/docs/guides/realtime?text-generation-quickstart-example=audio)，你可以创建一个由这些多模态LLM支持的语音助手。然而，如果你对开源库感兴趣，你也可以在本地环境中构建一个语音助手，并且不需要订阅专有的API！

# 为什么选择本地语音助手？

1.  数据隐私

1.  无API调用限制

1.  微调模型

首先，我相信大多数使用主流生成式AI聊天机器人的人都清楚他们的数据是通过这些服务器传输的。很多人可能会对数据隐私问题感到担忧……
