# LLM评估指标的高级指南

> 原文：[https://towardsdatascience.com/a-high-level-guide-to-llm-evaluation-metrics-fbecd08f725c?source=collection_archive---------6-----------------------#2024-02-27](https://towardsdatascience.com/a-high-level-guide-to-llm-evaluation-metrics-fbecd08f725c?source=collection_archive---------6-----------------------#2024-02-27)

## 发展对各种LLM基准和评分的理解，包括何时它们可能对你的目的有价值的直觉

[](https://dkhundley.medium.com/?source=post_page---byline--fbecd08f725c--------------------------------)[![David Hundley](../Images/1779ef96ec3d338f8fe4a9567ba7b194.png)](https://dkhundley.medium.com/?source=post_page---byline--fbecd08f725c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fbecd08f725c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fbecd08f725c--------------------------------) [David Hundley](https://dkhundley.medium.com/?source=post_page---byline--fbecd08f725c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fbecd08f725c--------------------------------) ·阅读时间17分钟·2024年2月27日

--

![](../Images/545d60b8b6ed5fb82e706c0b7eb16bdf.png)

标题卡由作者创建

几乎每周都会有一个新的大型语言模型（LLM）向公众发布。每次发布LLM时，这些提供商都会宣扬一些看起来相当令人印象深刻的性能数据。我发现的挑战是，这些新闻稿中引用的性能指标种类繁多。虽然有一些指标比其他指标出现得更频繁，但遗憾的是，并没有简单的“一两个”标准指标。如果你想看到一个实际的例子，[查看GPT-4性能的页面](https://openai.com/research/gpt-4)。它引用了许多不同的基准和评分！

第一个自然的问题是：“为什么我们不能简单地达成共识，使用一个单一的指标？”**简而言之，没有一种简单的方法可以评估LLM的性能，因此每个性能指标都试图为某个特定领域提供定量评估**。此外，许多这些性能指标还有“子指标”，这些子指标计算方式与原始指标略有不同。当我最初开始为这篇博客文章进行研究时，我的初衷是覆盖所有这些基准……
