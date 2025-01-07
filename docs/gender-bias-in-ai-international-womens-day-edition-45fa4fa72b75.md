# AI 中的性别偏见（国际妇女节版）

> 原文：[`towardsdatascience.com/gender-bias-in-ai-international-womens-day-edition-45fa4fa72b75?source=collection_archive---------8-----------------------#2024-03-08`](https://towardsdatascience.com/gender-bias-in-ai-international-womens-day-edition-45fa4fa72b75?source=collection_archive---------8-----------------------#2024-03-08)

## 关于 AI 中的性别偏见的简要概述与讨论

[](https://medium.com/@artfish?source=post_page---byline--45fa4fa72b75--------------------------------)![Yennie Jun](https://medium.com/@artfish?source=post_page---byline--45fa4fa72b75--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--45fa4fa72b75--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--45fa4fa72b75--------------------------------) [Yennie Jun](https://medium.com/@artfish?source=post_page---byline--45fa4fa72b75--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--45fa4fa72b75--------------------------------) ·阅读时间：13 分钟·2024 年 3 月 8 日

--

![](img/4be5c2e1ab6b7d6b3ef431b480c37d45.png)

由 Midjourney 创建

*本文最初发表于* [*art fish intelligence*](https://www.artfish.ai/p/gender-bias-in-ai-international-womens)

# 引言

为了庆祝国际妇女节，我想写一篇关于 AI 中性别偏见的短文。

AI 模型反映并且常常夸大了来自现实世界的性别偏见。量化模型中存在的偏见对于正确地处理和缓解这些偏见至关重要。

在本文中，我展示了一些重要的工作（以及目前正在进行的工作），旨在揭示、评估和衡量 AI 模型中不同方面的性别偏见。我还讨论了这些工作的意义，并强调了我注意到的一些空白。

# 但究竟什么是偏见呢？

所有这些术语（“性别”、“偏见”和“AI”）有时会被过度使用且含糊不清。

在 AI 研究的语境下，“性别”通常包括二元的男性/女性（因为计算机科学家更容易度量），偶尔会有“中性”类别。“AI”指的是基于人类创建数据训练的机器学习系统，包括像词嵌入这样的统计模型和基于 Transformer 的现代模型……
