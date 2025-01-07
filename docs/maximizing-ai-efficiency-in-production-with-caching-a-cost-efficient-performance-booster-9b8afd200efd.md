# 通过缓存最大化生产中的 AI 效率：一种成本高效的性能提升方案

> 原文：[`towardsdatascience.com/maximizing-ai-efficiency-in-production-with-caching-a-cost-efficient-performance-booster-9b8afd200efd?source=collection_archive---------6-----------------------#2024-03-19`](https://towardsdatascience.com/maximizing-ai-efficiency-in-production-with-caching-a-cost-efficient-performance-booster-9b8afd200efd?source=collection_archive---------6-----------------------#2024-03-19)

## 解锁缓存的力量，以利用 LangChain 缓存扩展 AI 解决方案——全面概述

[](https://medium.com/@han.heloir?source=post_page---byline--9b8afd200efd--------------------------------)![Han HELOIR, Ph.D. ☕️](https://medium.com/@han.heloir?source=post_page---byline--9b8afd200efd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9b8afd200efd--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9b8afd200efd--------------------------------) [Han HELOIR, Ph.D. ☕️](https://medium.com/@han.heloir?source=post_page---byline--9b8afd200efd--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9b8afd200efd--------------------------------) ·14 分钟阅读·2024 年 3 月 19 日

--

[免费朋友链接](https://medium.com/towards-data-science/maximizing-ai-efficiency-in-production-with-caching-a-cost-efficient-performance-booster-9b8afd200efd?sk=226f81b015f25d2cb70cb2f4ae859c93) — 请帮忙点赞[这篇 LinkedIn 文章](https://www.linkedin.com/posts/hanheloiryan_maximizing-ai-efficiency-in-production-with-activity-7175898095740694529-yVTD?utm_source=share&utm_medium=member_desktop)

# 引言

尽管人工智能应用具有变革性的潜力，但大约 70%的应用未能投入生产。挑战是什么？成本、性能、安全性、灵活性和可维护性。本文我们将讨论两个关键挑战：不断上升的成本和对高性能的需求——并揭示如何通过缓存策略解决这些问题，**这就是**解决方案。

![](img/8a0e842217cbf72dce7bdf9fcd66719f.png)

图片由[Possessed Photography](https://unsplash.com/@possessedphotography?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 成本挑战：规模与开销的对接

运行 AI 模型，特别是在大规模时，可能会变得极其昂贵。以 GPT-4 模型为例，处理 100 万输入 tokens 的费用为 30 美元，处理 100 万输出 tokens 的费用为 60 美元。这些费用很容易迅速累积，使得许多项目在财务上难以承担广泛的采用。

为了更好地理解这一点，我们可以考虑一个客服聊天机器人，每天处理大约**50,000 个用户查询**。每个查询和回复的平均长度可能是 50 个标记。一天之内，这就相当于 2,500,000 个标记，继续…
