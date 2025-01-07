# 多头自注意力 — 手动实现

> 原文：[`towardsdatascience.com/multi-headed-self-attention-by-hand-d2ce1ae031db?source=collection_archive---------4-----------------------#2024-07-12`](https://towardsdatascience.com/multi-headed-self-attention-by-hand-d2ce1ae031db?source=collection_archive---------4-----------------------#2024-07-12)

## 手动计算：现代人工智能的基石

[](https://medium.com/@danielwarfield1?source=post_page---byline--d2ce1ae031db--------------------------------)![Daniel Warfield](https://medium.com/@danielwarfield1?source=post_page---byline--d2ce1ae031db--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d2ce1ae031db--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d2ce1ae031db--------------------------------) [Daniel Warfield](https://medium.com/@danielwarfield1?source=post_page---byline--d2ce1ae031db--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d2ce1ae031db--------------------------------) ·6 分钟阅读·2024 年 7 月 12 日

--

![](img/659a613f94c1461109fe6e7b1e884c8e.png)

“焦点”由 Daniel Warfield 使用 MidJourney 创作。所有图片除非另有说明，否则均为作者提供。文章最初发布于[直观且详尽的解析](https://iaee.substack.com/)。

多头自注意力可能是机器学习中最重要的架构范式。本总结介绍了多头自注意力中的所有关键数学运算，使您能够在基础层面理解其内部工作原理。如果您想深入了解该主题背后的直觉，可以查看 IAEE 文章。

[](/transformers-intuitively-and-exhaustively-explained-58a5c5df8dbb?source=post_page-----d2ce1ae031db--------------------------------) ## Transformer — 直观且详尽的解析

### 探索现代机器学习浪潮：一步步拆解 Transformer

towardsdatascience.com

# 第一步：定义输入

多头自注意力（MHSA）在多种上下文中都有应用，每种应用可能会以不同的方式格式化输入。在自然语言处理的背景下，通常会使用词向量嵌入，并配以位置编码，来计算一个表示每个单词的向量。通常，无论数据类型如何，多头自注意力都会期望一系列向量，其中每个向量代表某种含义。
