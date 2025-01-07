# 搜索数据的三大策略

> 原文：[https://towardsdatascience.com/top-3-strategies-to-search-your-data-8b118dfd1350?source=collection_archive---------4-----------------------#2024-12-21](https://towardsdatascience.com/top-3-strategies-to-search-your-data-8b118dfd1350?source=collection_archive---------4-----------------------#2024-12-21)

## 从传统索引寻求到基于 AI 的语义搜索，每个软件工程师都应该知道的策略！

[](https://shawn-shi.medium.com/?source=post_page---byline--8b118dfd1350--------------------------------)[![Shawn Shi](../Images/f2125e0b312735f158e9930a7a4a6f83.png)](https://shawn-shi.medium.com/?source=post_page---byline--8b118dfd1350--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8b118dfd1350--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8b118dfd1350--------------------------------) [Shawn Shi](https://shawn-shi.medium.com/?source=post_page---byline--8b118dfd1350--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8b118dfd1350--------------------------------) ·阅读时长 7 分钟·2024年12月21日

--

## 背景 — 一切都是数据驱动的

你同意这个说法吗？技术已经深深融入我们日常生活的方方面面。我当然同意！

当你在亚马逊上购物时，你从数百万个商品中搜索，找到符合你期望的商品！当你在 Facebook、TikTok 或其他社交媒体应用上浏览时，你看到的内容非常有趣，难以停下来滚动。你是否曾经想过 Tinder 是如何找到匹配对象并将其推荐给数百万用户的？我最近读了一些关于推荐引擎的文章，来自*Tinder 技术博客*，从软件的角度来看，寻找朋友推荐的工作在大规模上是相当令人着迷的。

幕后，这些应用都依赖于高效地检索相关数据，以提供这些无缝的用户体验。

随着数据量的指数增长，**快速、准确且智能地搜索数据**的能力，成为现代软件工程中的一个关键组成部分。对于我们这些软件工程师和机器学习工程师来说，我们有工具可以利用，这样我们就可以应用正确的搜索策略，满足用户对速度和相关性的期望。无论是通过电子邮件查找用户资料，还是搜索文本标签，或其他…
