# 在不到10分钟内设置一个类似ChatGPT的本地界面+副驾驶

> 原文：[https://towardsdatascience.com/set-up-a-local-chatgpt-like-interface-copilot-in-less-than-10-minutes-60a02acd2628?source=collection_archive---------3-----------------------#2024-11-15](https://towardsdatascience.com/set-up-a-local-chatgpt-like-interface-copilot-in-less-than-10-minutes-60a02acd2628?source=collection_archive---------3-----------------------#2024-11-15)

## 使用Ollama、Llama3、Continue和Open WebUI提供一个安全、本地、开源和免费的虚拟助手体验

[](https://jerdibattista.medium.com/?source=post_page---byline--60a02acd2628--------------------------------)[![Jeremy DiBattista](../Images/dba9ce6fb3b04ee87e23df467a8d7ee4.png)](https://jerdibattista.medium.com/?source=post_page---byline--60a02acd2628--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--60a02acd2628--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--60a02acd2628--------------------------------) [Jeremy DiBattista](https://jerdibattista.medium.com/?source=post_page---byline--60a02acd2628--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--60a02acd2628--------------------------------) ·5分钟阅读·2024年11月15日

--

![](../Images/92a71cc5f501f699a12300eddad85394.png)

图片来自[Chris](https://unsplash.com/@chris23?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我经常告诉人们，我使用本地的LLM（大语言模型）来优化我的日常工作流，包括本地托管的类似ChatGPT的UI，以及免费的代码自动完成集成（类似于Copilot）到VSCode中，我最常听到的回答是“哇，那听起来像是要花费大量时间设置。”而我通常会反驳说，实际上我只花了几分钟！

从那里开始，为了帮助这个人，我会发送一组链接、说明和教程，所有这些都是为了处理那些可以如此快速完成的事情。这个过程将带你从头到尾完成，所花时间大约和你喝完一杯咖啡差不多。

本教程将分为两个部分，一个是Web UI部分，另一个是代码助手部分（因为不是每个人都会对这一部分感兴趣）。如果你对VSCode集成感兴趣，请先完成Web UI部分，然后继续！

## 为什么选择本地部署？

如果你有商业机密、专有代码或正在使用你不希望泄露的私人密钥和个人身份信息（PII），你不希望这些信息变得…
