# 多模态RAG — 直观且详尽的解释

> 原文：[https://towardsdatascience.com/multimodal-rag-intuitively-and-exhaustively-explained-5713d8069eb0?source=collection_archive---------4-----------------------#2024-07-25](https://towardsdatascience.com/multimodal-rag-intuitively-and-exhaustively-explained-5713d8069eb0?source=collection_archive---------4-----------------------#2024-07-25)

## 人工智能 | 检索增强生成 | 多模态

## 现代RAG适用于现代模型。

[](https://medium.com/@danielwarfield1?source=post_page---byline--5713d8069eb0--------------------------------)[![Daniel Warfield](../Images/c1c8b4dd514f6813e08e401401324bca.png)](https://medium.com/@danielwarfield1?source=post_page---byline--5713d8069eb0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5713d8069eb0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5713d8069eb0--------------------------------) [Daniel Warfield](https://medium.com/@danielwarfield1?source=post_page---byline--5713d8069eb0--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5713d8069eb0--------------------------------) ·10分钟阅读·2024年7月25日

--

![](../Images/439b5f04f55575e85c358964da8b2e91.png)

“多彩团队”由Daniel Warfield使用Midjourney创作。除非另有说明，所有图片均由作者提供。文章最初发布于[直观且详尽的解释](https://iaee.substack.com/)。

多模态检索增强生成（Multimodal Retrieval Augmented Generation，简称RAG）是一种新兴的设计范式，它使得AI模型能够与文本、图像、视频等多种信息存储库进行交互。

在探讨这个话题时，我们将首先介绍什么是检索增强生成（RAG）、多模态的概念，以及这两者如何结合在一起，构建现代的多模态RAG系统。一旦我们理解了多模态RAG的基本概念，就可以使用Google Gemini和CLIP风格模型进行编码，亲自构建一个多模态RAG系统。

**这对谁有用？** 任何对现代人工智能感兴趣的人。

**这篇文章的难度如何？** 尽管多模态RAG处于人工智能的前沿，但它直观简单且易于接触。本文应该对资深AI研究人员有趣，同时对于初学者来说也足够简单。

**前提条件：** 无

# 检索增强生成简介

在深入了解多模态RAG之前，先简要回顾一下传统的检索增强生成（RAG）。基本概念是……
