# 面向初学者的LLM简介

> 原文：[https://towardsdatascience.com/a-beginner-friendly-introduction-to-llms-1b8dbdd628a0?source=collection_archive---------2-----------------------#2024-05-10](https://towardsdatascience.com/a-beginner-friendly-introduction-to-llms-1b8dbdd628a0?source=collection_archive---------2-----------------------#2024-05-10)

## 大型语言模型的第一步

[](https://medium.com/@Chim-SO?source=post_page---byline--1b8dbdd628a0--------------------------------)[![Chayma Zatout](../Images/341c45f53ddf73dc0851d547cc7cb55a.png)](https://medium.com/@Chim-SO?source=post_page---byline--1b8dbdd628a0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1b8dbdd628a0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1b8dbdd628a0--------------------------------) [Chayma Zatout](https://medium.com/@Chim-SO?source=post_page---byline--1b8dbdd628a0--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1b8dbdd628a0--------------------------------) ·13分钟阅读·2024年5月10日

--

![](../Images/9fc3aecd797485d661901acf514840d2.png)

图片来源：[okeykat](https://unsplash.com/@okeykat?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我一直想写一篇关于大型语言模型（LLM）的教程，之后我一直在思考如何编写一系列适合初学者的文章，帮助大家理解并入门LLM。在这篇文章中，我将尝试提供一个面向初学者的LLM简介，并以简单的方式解释关键概念，而不深入探讨技术细节。我的希望是，阅读完这篇文章后，你能更加自信地阅读关于LLM的更高级文档。

不是Medium会员？没关系！继续阅读通过这个[*朋友链接*](/a-beginner-friendly-introduction-to-llms-1b8dbdd628a0?sk=6f2feaf595e109a87a966bc8ab6649e5)。

***目录***

· [1\. 引言](#2ca4)

· [2\. LLM的定义](#1390)

· [3\. 一些LLM](#7aea)

∘ [3.1\. BERT家族](#00f5)

∘ [3.2\. GPT家族](#3ee5)

∘ [3.3\. PaLM家族](#419f)

∘ [3.4\. LLaMA家族](#8384)

· [4\. LLM系统](#2f32)

∘ [4.1\. 一般架构](#bab6)

∘ [4.2\. 训练过程](#d7c8)

∘ [4.3\. LLM的输入与输出](#cedb)

· [5\. 使用场景](#1116)

· [6\. 如何调整LLM](#1884)

∘ [6.1\. 微调](#164c)

∘ [6.2\. 提示词设计](#a112)

· [7\. 挑战](#d6c3)

· [8\. 结论](#9255)
