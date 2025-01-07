# Claude Sonnet 3.5 系统提示的法医分析

> 原文：[https://towardsdatascience.com/a-forensic-analysis-of-the-claude-sonnet-3-5-system-prompt-leak-1d2e7170e2fa?source=collection_archive---------3-----------------------#2024-06-24](https://towardsdatascience.com/a-forensic-analysis-of-the-claude-sonnet-3-5-system-prompt-leak-1d2e7170e2fa?source=collection_archive---------3-----------------------#2024-06-24)

## Claude 3.5 Sonnet 伪造物对于结构化输出（如代码生成）就像向量检索对于RAG一样。

[](https://ed-burton.medium.com/?source=post_page---byline--1d2e7170e2fa--------------------------------)[![Edward Burton](../Images/75418d9af27ffa72778eeb76bb1642fa.png)](https://ed-burton.medium.com/?source=post_page---byline--1d2e7170e2fa--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1d2e7170e2fa--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1d2e7170e2fa--------------------------------) [Edward Burton](https://ed-burton.medium.com/?source=post_page---byline--1d2e7170e2fa--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1d2e7170e2fa--------------------------------) ·7分钟阅读·2024年6月24日

--

![](../Images/68cda1865a3749423251418747c26e97.png)

# 引入伪造物

*结构化输出生成的一大进步。*

这篇文章分析了[Claude 3.5 Sonnet](https://www.anthropic.com/news/claude-3-5-sonnet)系统提示的生成。本文末尾提供了该分析代码的链接，文章与源代码一同发布。该分析的主要部分涉及伪造物的引入，以及这一过程如何作为Anthropic目前在代码生成和其他任务中超越所有LLM基准的分类与检索系统的一部分。

> “伪造物用于包含大量、可独立使用且用户可能修改或重用的内容。”

伪造物是一种范式转变，因为它正式化了一个新概念：持久数据的概念。伪造物是LLM将要完成的每项任务的基础对象或示例。通过持久化数据，我们能够进行迭代并记住变化。

通过提供固定的参考，我们解锁了逐步改进和精炼输出的能力。这是朝着控制冗长LLM输出的短暂性质并生成标准化输出的一步。
