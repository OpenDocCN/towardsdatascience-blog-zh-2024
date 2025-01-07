# 评估大型语言模型

> 原文：[`towardsdatascience.com/evaluating-large-language-models-a145b801dce0?source=collection_archive---------1-----------------------#2024-01-14`](https://towardsdatascience.com/evaluating-large-language-models-a145b801dce0?source=collection_archive---------1-----------------------#2024-01-14)

## 生成式人工智能

## 如何评估你的大型语言模型（LLM）表现如何？一份完整的指南。

[](https://michaloleszak.medium.com/?source=post_page---byline--a145b801dce0--------------------------------)![Michał Oleszak](https://michaloleszak.medium.com/?source=post_page---byline--a145b801dce0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a145b801dce0--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a145b801dce0--------------------------------) [Michał Oleszak](https://michaloleszak.medium.com/?source=post_page---byline--a145b801dce0--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a145b801dce0--------------------------------) ·19 分钟阅读·2024 年 1 月 14 日

--

![](img/e5f31c4eb999520c1f49d4d0f136d2d1.png)

自从稳定扩散（Stable Diffusion）和 ChatGPT 发布超过一年以来，生成式人工智能发展迅速。每周几乎都会有新的模型宣布声称能超越现有最先进的技术。但我们如何知道这些模型是否真有其价值？在缺乏地面真实数据和“正确”解决方案的情况下，如何比较和排名生成式模型？最后，如果大型语言模型（LLM）通过检索增强生成（Retrieval-Augmented Generation，简称 RAG）系统使用外部数据，我们又如何评判它是否正确使用了这些数据？

在这两部分的系列文章中，我们将探讨生成式人工智能的评估协议。本文重点讨论文本生成和大型语言模型。敬请关注下一篇，我们将讨论图像生成器的评估方法。

# 评估生成的内容

让我们首先注意到生成式模型和判别式模型之间的区别。生成式模型生成与训练数据相似的新数据样本，无论是文本、图像、音频、视频、潜在表示，甚至是表格数据。另一方面，判别式模型通过训练数据学习决策边界，使我们能够解决…
