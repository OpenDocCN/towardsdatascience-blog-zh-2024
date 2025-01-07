# 负责任的 LLMOps

> 原文：[`towardsdatascience.com/responsible-llmops-985cd1af3639?source=collection_archive---------2-----------------------#2024-06-16`](https://towardsdatascience.com/responsible-llmops-985cd1af3639?source=collection_archive---------2-----------------------#2024-06-16)

## 将负责任的 AI 实践融入 LLMOps

[](https://debmalyabiswas.medium.com/?source=post_page---byline--985cd1af3639--------------------------------)![Debmalya Biswas](https://debmalyabiswas.medium.com/?source=post_page---byline--985cd1af3639--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--985cd1af3639--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--985cd1af3639--------------------------------) [Debmalya Biswas](https://debmalyabiswas.medium.com/?source=post_page---byline--985cd1af3639--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--985cd1af3639--------------------------------) ·阅读时间 15 分钟·2024 年 6 月 16 日

--

**摘要**。*虽然我们看到 LLMOps 和负责任 AI 实践在生成 AI 实现中的日益采用，但讨论通常发生在不同的社区中，检查清单和它们的下游实施/工具之间存在很大的脱节。

在本文中，我们旨在通过统一的方法将这两个框架结合起来，目的是以可扩展和负责任的方式部署使用案例。我们突出展示 LLM 特定的问题，深入探讨相关的 LLMOps 架构模式，并提出解决方案，以建立一个由负责任 AI 实践治理的集成 LLMOps 平台。*

与[**DIPTA CHAKRABORTY**](https://medium.com/u/c6c882e46208?source=post_page---user_mention--985cd1af3639--------------------------------)**、**[**Bhargav Mitra**](https://www.linkedin.com/in/bhargav-mitra/)合作

# 介绍

我们看到对大语言模型（**LLMs**）及其颠覆企业使用案例的潜力越来越感到兴奋。然而，今天的探索大多局限于概念验证（POC）。

> 要真正实现生成 AI 使用案例在企业中的规模化，需要建立一个具备 LLMOps 能力并具有适当 LLM 治理的可扩展 LLM 平台。

与此同时，我们也看到越来越多的关注点放在了负责任地部署 AI 产品上，因为有很多 LLM 生成有毒、带偏见、性别化和暴力内容的案例。LLM 还被发现会出现幻觉——提供带有偏见和误导性的回应。

> 为了缓解这些问题，…
