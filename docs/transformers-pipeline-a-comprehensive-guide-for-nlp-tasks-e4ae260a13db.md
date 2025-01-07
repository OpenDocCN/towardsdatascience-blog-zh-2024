# Transformers 流程：NLP 任务的综合指南

> 原文：[`towardsdatascience.com/transformers-pipeline-a-comprehensive-guide-for-nlp-tasks-e4ae260a13db?source=collection_archive---------4-----------------------#2024-02-16`](https://towardsdatascience.com/transformers-pipeline-a-comprehensive-guide-for-nlp-tasks-e4ae260a13db?source=collection_archive---------4-----------------------#2024-02-16)

## 深入探讨那行代码，它可以将成千上万的现成 AI 解决方案带入你的脚本，并充分利用 🤗 Transformers 库的强大功能。

[](https://medium.com/@george.stavrakis.1996?source=post_page---byline--e4ae260a13db--------------------------------)![George Stavrakis](https://medium.com/@george.stavrakis.1996?source=post_page---byline--e4ae260a13db--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e4ae260a13db--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e4ae260a13db--------------------------------) [George Stavrakis](https://medium.com/@george.stavrakis.1996?source=post_page---byline--e4ae260a13db--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e4ae260a13db--------------------------------) ·19 分钟阅读·2024 年 2 月 16 日

--

![](img/8328d012a20db0cf025fb5b6e8e4ce79.png)

摄影：由[Simon Kadula](https://unsplash.com/@simonkadula?utm_source=medium&utm_medium=referral)提供，图片来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍

不同形式和方式下使用的人类语言可以产生大量信息，但这种信息往往是非结构化的。人类天生就有交流和表达自己意见与观点的需求，尤其在当今这个拥有多种渠道进行表达的时代。这导致了大量非结构化数据的产生，而这些数据迄今为止在商业上仍未得到充分或有效的利用。

然而，近年来，发生了显著的变化。

人工智能（AI）领域的快速发展，特别是在自然语言处理（NLP）领域，使我们能够以编程方式理解并与这些信息进行互动，促使许多企业重新审视这一知识来源，并将其作为新产品的动力。

这一变化的催化剂是 ChatGPT 的发布，它向世界展示了变换器模型的有效性，并且普及了大规模语言模型（LLM）这一领域。
