# Vision Transformer终极指南

> 原文：[https://towardsdatascience.com/the-ultimate-guide-to-vision-transformers-0a6df32cb248?source=collection_archive---------8-----------------------#2024-08-30](https://towardsdatascience.com/the-ultimate-guide-to-vision-transformers-0a6df32cb248?source=collection_archive---------8-----------------------#2024-08-30)

## 《Vision Transformer（ViT）全面指南》，它革新了计算机视觉

[](https://medium.com/@francoisporcher?source=post_page---byline--0a6df32cb248--------------------------------)[![François Porcher](../Images/9ddb233f8cadbd69026bd79e2bd62dea.png)](https://medium.com/@francoisporcher?source=post_page---byline--0a6df32cb248--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0a6df32cb248--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0a6df32cb248--------------------------------) [François Porcher](https://medium.com/@francoisporcher?source=post_page---byline--0a6df32cb248--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0a6df32cb248--------------------------------) ·阅读时间：7分钟·2024年8月30日

--

大家好！对于还不认识我的朋友们，我叫Francois，是Meta的研究科学家。我热衷于解释先进的AI概念，并使其更加易懂。

今天，我们来深入探讨计算机视觉领域的一项重大贡献：**Vision Transformer（ViT）**。

![](../Images/2008420b7885ebccbdfac2394b8c4f6d.png)

将图像转换为图块，图像来源：作者

# 先来一点历史背景……

Vision Transformer（ViT）由Alexey Dosovitskiy及其团队（Google Brain）在2021年提出，相关论文为[《一张图胜过16×16个词》](https://arxiv.org/abs/2010.11929)。当时，Transformers已证明是解锁NLP任务卓越性能的关键，且这一模型首次在2017年的标志性论文[《Attention is All You Need》](https://arxiv.org/abs/1706.03762)中提出。

在2017到2021年间，已有多次尝试将注意力机制整合到卷积神经网络（CNN）中。然而，这些大多是混合模型（结合了CNN层和注意力层），并且缺乏可扩展性。Google通过完全去除卷积操作，利用其计算能力来扩展模型，解决了这一问题。

# 这篇文章所回答的百万美元问题是……
