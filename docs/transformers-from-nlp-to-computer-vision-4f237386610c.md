# Transformers：从自然语言处理到计算机视觉

> 原文：[`towardsdatascience.com/transformers-from-nlp-to-computer-vision-4f237386610c?source=collection_archive---------1-----------------------#2024-05-05`](https://towardsdatascience.com/transformers-from-nlp-to-computer-vision-4f237386610c?source=collection_archive---------1-----------------------#2024-05-05)

## Transformer 架构是如何适应计算机视觉任务的

[](https://medium.com/@vuphuongthao9611?source=post_page---byline--4f237386610c--------------------------------)![Thao Vu](https://medium.com/@vuphuongthao9611?source=post_page---byline--4f237386610c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4f237386610c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4f237386610c--------------------------------) [Thao Vu](https://medium.com/@vuphuongthao9611?source=post_page---byline--4f237386610c--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4f237386610c--------------------------------) ·阅读时间：7 分钟·2024 年 5 月 5 日

--

![](img/137ff68fa22ff9d7db4503dc87f98d3a.png)

由[kyler trautner](https://unsplash.com/@kylertrautner?utm_source=medium&utm_medium=referral)拍摄，图片来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 引言

2017 年，论文《Attention is all you need》[1]引起了 NLP 研究界的轰动。至今已被引用超过 10 万次，它的 Transformer 成为了如今大多数主流 NLP 架构的基石。要了解 Transformer 在 NLP 领域的重要成果，可以查看我之前的文章。

[](/bert-vs-gpt-comparing-the-nlp-giants-329d105e34ec?source=post_page-----4f237386610c--------------------------------) ## BERT 与 GPT：比较 NLP 巨头

### 它们的结构有何不同，这些差异如何影响模型的能力？

towardsdatascience.com

与此同时，由于计算机视觉（CV）长期以来由 CNN 主导，Transformer 在该领域的应用直到最近才有所突破。本文将讨论将 Transformer 应用于计算机视觉所面临的挑战，以及计算机视觉研究人员如何调整这些模型。

# 挑战

## 分词

分词文本序列已经被长期研究，采用各种优化方法来概括和适应未见过的文本。然而，所有这些努力都依赖于将字符和术语作为单位来处理的思想。
