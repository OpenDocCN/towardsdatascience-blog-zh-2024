# 通过少量示例以无训练方式提升 CLIP 性能

> 原文：[`towardsdatascience.com/improving-clip-performance-in-training-free-manner-with-few-shot-examples-a59f6b29cdc8?source=collection_archive---------16-----------------------#2024-01-30`](https://towardsdatascience.com/improving-clip-performance-in-training-free-manner-with-few-shot-examples-a59f6b29cdc8?source=collection_archive---------16-----------------------#2024-01-30)

## 第三部分 — 使用 Tip-Adapter 对零样本分类的简单扩展

[](https://medium.com/@alexml0123?source=post_page---byline--a59f6b29cdc8--------------------------------)![Alexey Kravets](https://medium.com/@alexml0123?source=post_page---byline--a59f6b29cdc8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a59f6b29cdc8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a59f6b29cdc8--------------------------------) [Alexey Kravets](https://medium.com/@alexml0123?source=post_page---byline--a59f6b29cdc8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a59f6b29cdc8--------------------------------) ·阅读时间 7 分钟·2024 年 1 月 30 日

--

这是关于如何提升 CLIP 分类性能的第三篇文章。你可以在[这里](https://medium.com/towards-data-science/simple-way-of-improving-zero-shot-clip-performance-4eae474cb447)找到第一篇，[这里](https://medium.com/towards-data-science/improving-performance-and-explainability-of-zero-shot-clip-33e579d3f4bb)找到第二篇。在前两篇文章中，我们的重点是零样本分类，我们发现利用大型语言模型（LLM）定制提示可以提高 CLIP 的零样本分类性能。在本文中，我们将探索如何在为每个类别提供少量视觉示例的情况下，进一步提升 CLIP 的分类性能。在继续之前，我建议你先回顾一下我系列中的第一篇文章，以加深对 CLIP 的理解。

# 引言

CLIP 的零样本分类能力受到其在预训练期间获得的知识的限制。因此，如果我们想对在 CLIP 预训练数据中稀缺或不存在的数据进行分类，分类性能可能会不尽如人意。尽管构建一个广泛的数据集可能具有挑战性，但通常可以为每个类别获取少量示例。提升 CLIP 性能的一种方法是，在其上方加上小型适配器，并使用少量示例图像进行训练，同时保持 CLIP 的原始权重冻结。然而，在某些情况下，训练即使是小型适配器也可能不可行。作为替代，我们可以以无训练的方式利用 CLIP，同时仍然…
