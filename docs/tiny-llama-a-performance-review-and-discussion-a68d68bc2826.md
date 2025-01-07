# TinyLlama——强大的小型语言模型的有希望的未来

> 原文：[https://towardsdatascience.com/tiny-llama-a-performance-review-and-discussion-a68d68bc2826?source=collection_archive---------5-----------------------#2024-04-20](https://towardsdatascience.com/tiny-llama-a-performance-review-and-discussion-a68d68bc2826?source=collection_archive---------5-----------------------#2024-04-20)

## 了解TinyLlama，一个小型语言模型，能够在较少的计算资源下完成多种复杂任务

[](https://oieivind.medium.com/?source=post_page---byline--a68d68bc2826--------------------------------)[![Eivind Kjosbakken](../Images/5f91b74428e1202fc4a176a3dd1cb1c7.png)](https://oieivind.medium.com/?source=post_page---byline--a68d68bc2826--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a68d68bc2826--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a68d68bc2826--------------------------------) [Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--a68d68bc2826--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a68d68bc2826--------------------------------) ·10分钟阅读·2024年4月20日

--

TinyLlama是一个开源项目，旨在训练一个约为11亿参数的小型语言模型。该项目的目标是创建一个能够完成类似Llama 2这类完整LLM所能实现任务的语言模型，但占用更少的内存。本文将讨论如何在本地计算机上实现并运行TinyLlama模型。此外，还将讨论TinyLlama当前的性能，以及其优缺点。

![](../Images/305119936432aa90313172421cf45caf.png)

ChatGPT对TinyLlama模型的构想。OpenAI。（2024年）。*ChatGPT*（4）[大型语言模型]。[https://chat.openai.com](https://chat.openai.com)

# 目录

· [目录](#a4a9)

· [动机](#aa74)

· [在本地实现模型](#051b)

· [测试模型](#d239)

∘ [斐波那契数列](#dc58)

∘ [RAG](#2509)

∘ [生成对话](#879b)

∘ [使用TinyLlama编程](#b65c)

· [我对该模型的看法](#da52)

· [结论](#dc79)

# 动机

我撰写这篇文章的动机是跟上机器学习的最新趋势。虽然TinyLlama发布已有一段时间……
