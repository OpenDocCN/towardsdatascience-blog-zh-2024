# 大型语言模型，GPT-3：语言模型是少样本学习者

> 原文：[`towardsdatascience.com/large-language-models-gpt-3-language-models-are-few-shot-learners-6e1261a1b466?source=collection_archive---------6-----------------------#2024-02-16`](https://towardsdatascience.com/large-language-models-gpt-3-language-models-are-few-shot-learners-6e1261a1b466?source=collection_archive---------6-----------------------#2024-02-16)

## 在元学习框架内高效地将 GPT 从大规模扩展到超大规模

[](https://medium.com/@slavahead?source=post_page---byline--6e1261a1b466--------------------------------)![Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--6e1261a1b466--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6e1261a1b466--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6e1261a1b466--------------------------------) [Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--6e1261a1b466--------------------------------)

· 发表在 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6e1261a1b466--------------------------------) · 阅读时间：8 分钟·2024 年 2 月 16 日

--

![](img/232803ab4c437443653fc59767adaf6f.png)

# 引言

**GPT** 是一系列最近受到广泛关注的语言模型。2020 年 GPT-3 的发布迅速吸引了数据科学社区的目光。在 GPT-2 出现后，几乎没人能够预见到，差不多一年后会出现一个包含 **1750 亿参数** 的超大规模版本！相比其前身，这个模型大了两个数量级。

GPT-3 的巨大能力使其可以应用于各种日常场景：代码补全、文章写作、内容创作、虚拟助手等。尽管这些任务的质量并不总是完美，但 GPT-3 所取得的整体进展无疑是令人震惊的！

在本文中，我们将详细探讨 GPT-3 的主要细节，以及受到 GPT-2 创作者启发的有用想法。在整个探索过程中，我们将参考 [官方的 GPT-3 论文](https://arxiv.org/pdf/2005.14165.pdf)。值得注意的是，包括数据收集、架构选择和预训练过程在内的大部分 GPT-3 设置，都是直接来自于 GPT-2。因此，大多数时候我们将会…
