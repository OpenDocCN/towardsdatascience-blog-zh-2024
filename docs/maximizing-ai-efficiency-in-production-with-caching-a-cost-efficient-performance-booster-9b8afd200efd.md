# 通过缓存最大化生产中的AI效率：一种成本高效的性能提升方案

> 原文：[https://towardsdatascience.com/maximizing-ai-efficiency-in-production-with-caching-a-cost-efficient-performance-booster-9b8afd200efd?source=collection_archive---------6-----------------------#2024-03-19](https://towardsdatascience.com/maximizing-ai-efficiency-in-production-with-caching-a-cost-efficient-performance-booster-9b8afd200efd?source=collection_archive---------6-----------------------#2024-03-19)

## 解锁缓存的力量，以利用LangChain缓存扩展AI解决方案——全面概述

[](https://medium.com/@han.heloir?source=post_page---byline--9b8afd200efd--------------------------------)[![Han HELOIR, Ph.D. ☕️](../Images/53c132b64fda2f1d9ebd6af6d582d24c.png)](https://medium.com/@han.heloir?source=post_page---byline--9b8afd200efd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9b8afd200efd--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9b8afd200efd--------------------------------) [Han HELOIR, Ph.D. ☕️](https://medium.com/@han.heloir?source=post_page---byline--9b8afd200efd--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9b8afd200efd--------------------------------) ·14分钟阅读·2024年3月19日

--

[免费朋友链接](https://medium.com/towards-data-science/maximizing-ai-efficiency-in-production-with-caching-a-cost-efficient-performance-booster-9b8afd200efd?sk=226f81b015f25d2cb70cb2f4ae859c93) — 请帮忙点赞[这篇LinkedIn文章](https://www.linkedin.com/posts/hanheloiryan_maximizing-ai-efficiency-in-production-with-activity-7175898095740694529-yVTD?utm_source=share&utm_medium=member_desktop)

# 引言

尽管人工智能应用具有变革性的潜力，但大约70%的应用未能投入生产。挑战是什么？成本、性能、安全性、灵活性和可维护性。本文我们将讨论两个关键挑战：不断上升的成本和对高性能的需求——并揭示如何通过缓存策略解决这些问题，**这就是**解决方案。

![](../Images/8a0e842217cbf72dce7bdf9fcd66719f.png)

图片由[Possessed Photography](https://unsplash.com/@possessedphotography?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 成本挑战：规模与开销的对接

运行AI模型，特别是在大规模时，可能会变得极其昂贵。以GPT-4模型为例，处理100万输入tokens的费用为30美元，处理100万输出tokens的费用为60美元。这些费用很容易迅速累积，使得许多项目在财务上难以承担广泛的采用。

为了更好地理解这一点，我们可以考虑一个客服聊天机器人，每天处理大约**50,000个用户查询**。每个查询和回复的平均长度可能是50个标记。一天之内，这就相当于2,500,000个标记，继续…
