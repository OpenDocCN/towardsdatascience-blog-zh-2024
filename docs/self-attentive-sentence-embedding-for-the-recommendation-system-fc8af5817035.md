# 自注意力句子嵌入在推荐系统中的应用

> 原文：[`towardsdatascience.com/self-attentive-sentence-embedding-for-the-recommendation-system-fc8af5817035?source=collection_archive---------10-----------------------#2024-05-22`](https://towardsdatascience.com/self-attentive-sentence-embedding-for-the-recommendation-system-fc8af5817035?source=collection_archive---------10-----------------------#2024-05-22)

## 什么是自注意力嵌入，如何在推荐系统中使用它？

[](https://medium.com/@bobi_29852?source=post_page---byline--fc8af5817035--------------------------------)![Bào Bùi](https://medium.com/@bobi_29852?source=post_page---byline--fc8af5817035--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fc8af5817035--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fc8af5817035--------------------------------) [Bào Bùi](https://medium.com/@bobi_29852?source=post_page---byline--fc8af5817035--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fc8af5817035--------------------------------) ·6 分钟阅读·2024 年 5 月 22 日

--

![](img/ed3ccab6483e5f59165ac277457e7f19.png)

图片由[Giulia Bertelli](https://unsplash.com/@giulia_bertelli)提供，来源于[Unsplash](https://unsplash.com/photos/j_luAxi8fWc)

# 引言

Transformer 层及其注意力机制是自然语言处理（NLP）领域最具影响力的思想之一。它们在许多大型语言模型中发挥着至关重要的作用，如[ChatGPT](https://openai.com/blog/chatgpt)和[LLaMA](https://ai.meta.com/blog/large-language-model-llama-meta-ai/)，这些模型最近在全球范围内引起了轰动。

然而，还有一个来自 NLP 社区的有趣想法，它的影响主要体现在推荐领域：自注意力句子嵌入。在本文中，我将带领大家了解自注意力句子嵌入[1]及其在推荐系统中的应用。

# 它是如何工作的

## 总体思路

本文的主要思想是找到一种更好的方式，将句子编码为多个嵌入，捕捉句子的不同方面。具体而言，作者希望将句子编码为一个二维矩阵，而不是单一的嵌入，其中每一行的嵌入捕捉句子的不同方面：

一旦我们获得了句子嵌入，就可以将它们用于各种下游任务，例如句子分析、作者画像等。
