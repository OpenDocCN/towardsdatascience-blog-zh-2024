# 面向初学者的 LLM 简介

> 原文：[`towardsdatascience.com/a-beginner-friendly-introduction-to-llms-1b8dbdd628a0?source=collection_archive---------2-----------------------#2024-05-10`](https://towardsdatascience.com/a-beginner-friendly-introduction-to-llms-1b8dbdd628a0?source=collection_archive---------2-----------------------#2024-05-10)

## 大型语言模型的第一步

[](https://medium.com/@Chim-SO?source=post_page---byline--1b8dbdd628a0--------------------------------)![Chayma Zatout](https://medium.com/@Chim-SO?source=post_page---byline--1b8dbdd628a0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1b8dbdd628a0--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1b8dbdd628a0--------------------------------) [Chayma Zatout](https://medium.com/@Chim-SO?source=post_page---byline--1b8dbdd628a0--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1b8dbdd628a0--------------------------------) ·13 分钟阅读·2024 年 5 月 10 日

--

![](img/9fc3aecd797485d661901acf514840d2.png)

图片来源：[okeykat](https://unsplash.com/@okeykat?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我一直想写一篇关于大型语言模型（LLM）的教程，之后我一直在思考如何编写一系列适合初学者的文章，帮助大家理解并入门 LLM。在这篇文章中，我将尝试提供一个面向初学者的 LLM 简介，并以简单的方式解释关键概念，而不深入探讨技术细节。我的希望是，阅读完这篇文章后，你能更加自信地阅读关于 LLM 的更高级文档。

不是 Medium 会员？没关系！继续阅读通过这个*朋友链接*。

***目录***

· 1\. 引言

· 2\. LLM 的定义

· 3\. 一些 LLM

∘ 3.1\. BERT 家族

∘ 3.2\. GPT 家族

∘ 3.3\. PaLM 家族

∘ 3.4\. LLaMA 家族

· 4\. LLM 系统

∘ 4.1\. 一般架构

∘ 4.2\. 训练过程

∘ 4.3\. LLM 的输入与输出

· 5\. 使用场景

· 6\. 如何调整 LLM

∘ 6.1\. 微调

∘ 6.2\. 提示词设计

· 7\. 挑战

· 8\. 结论
