# AutoHyDE：使 HyDE 在高级 LLM RAG 中更加出色

> 原文：[https://towardsdatascience.com/autohyde-making-hyde-better-for-advanced-llm-rag-619e58cdbd8e?source=collection_archive---------0-----------------------#2024-04-04](https://towardsdatascience.com/autohyde-making-hyde-better-for-advanced-llm-rag-619e58cdbd8e?source=collection_archive---------0-----------------------#2024-04-04)

## 🔎 深入探讨 HyDE 在高级 LLM RAG 中的应用 + 💡 介绍 AutoHyDE，一种半监督框架，旨在提高 HyDE 的效果、覆盖面和适用性

[](https://ianhojy.medium.com/?source=post_page---byline--619e58cdbd8e--------------------------------)[![Ian Ho](../Images/1b56c25ee3bedfb5c7369d4bfc93aa91.png)](https://ianhojy.medium.com/?source=post_page---byline--619e58cdbd8e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--619e58cdbd8e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--619e58cdbd8e--------------------------------) [Ian Ho](https://ianhojy.medium.com/?source=post_page---byline--619e58cdbd8e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--619e58cdbd8e--------------------------------) ·阅读时间 19 分钟 ·2024年4月4日

--

![](../Images/5e5d67d7b328dcb0334146a63e6ba258.png)

图片由作者与 DALL-E 协助制作

# **介绍**

在检索增强生成（RAG）领域，**假设文档嵌入**（HyDE）已被证明是一种强大的查询重写方法，有助于提高检索文档的相关性。

对于未接触过的读者，传统的检索方法仅使用原始输入生成嵌入向量进行检索，而 HyDE 是一种生成嵌入向量的方法，这些向量与索引文档的嵌入空间更为相关，以便进行更精准的文档检索。

高层次的总结是：（1）从用户输入创建假设文档，（2）将假设文档转换为嵌入，（3）使用嵌入进行相似文档的检索

我一直在一些工作和个人项目中使用 RAG 和基础 HyDE，经过一段时间的使用，我意识到现有的 HyDE 实现并不是总能开箱即用，也不像我预期的那样灵活。因此，在进行了一些方法学研究并深入阅读论文和源代码后……
