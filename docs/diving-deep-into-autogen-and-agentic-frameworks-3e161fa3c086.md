# 深入探讨 AutoGen 与多智能体框架

> 原文：[`towardsdatascience.com/diving-deep-into-autogen-and-agentic-frameworks-3e161fa3c086?source=collection_archive---------4-----------------------#2024-06-28`](https://towardsdatascience.com/diving-deep-into-autogen-and-agentic-frameworks-3e161fa3c086?source=collection_archive---------4-----------------------#2024-06-28)

## 本文将深入探讨《AutoGen: 通过多智能体对话实现下一代大型语言模型应用》论文以及 AutoGen 项目的细节

[](https://medium.com/@mgunton7?source=post_page---byline--3e161fa3c086--------------------------------)![Matthew Gunton](https://medium.com/@mgunton7?source=post_page---byline--3e161fa3c086--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3e161fa3c086--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3e161fa3c086--------------------------------) [Matthew Gunton](https://medium.com/@mgunton7?source=post_page---byline--3e161fa3c086--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3e161fa3c086--------------------------------) ·11 分钟阅读·2024 年 6 月 28 日

--

![](img/4139c2cd244cc4350a926ece5265cdb5.png)

作者图片 — SDXL

近期，智能体成为技术新闻网站的热议话题。尽管人们对这些程序的潜力抱有很大期望，但关于应该支持这些智能体的框架却鲜有讨论。从高层次来看，智能体不过是一个程序，通常由大型语言模型（LLM）驱动，执行某种操作。虽然任何人都可以向 LLM 发出提示，但智能体系统的关键区别在于它们在面对模糊任务时的持续表现。

实现这种持续的表现并非易事。尽管像思维链、反思等提示技术已被证明能提高 LLM 的表现，但 LLM 在聊天过程中接受适当反馈时，往往会显著改善。这可能表现为科学家指出聊天机器人回答中的缺陷，或程序员在尝试运行 LLM 代码时复制编译器消息。

因此，在我们努力使这些智能体系统的表现更加稳定时，人们可能会合理地问，是否可以找到一种方法，让多个 LLM 相互反馈，从而……
