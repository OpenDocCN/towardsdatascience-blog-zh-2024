# 文本嵌入、分类和语义搜索

> 原文：[https://towardsdatascience.com/text-embeddings-classification-and-semantic-search-8291746220be?source=collection_archive---------3-----------------------#2024-03-27](https://towardsdatascience.com/text-embeddings-classification-and-semantic-search-8291746220be?source=collection_archive---------3-----------------------#2024-03-27)

## 带有示例 Python 代码的介绍

[](https://shawhin.medium.com/?source=post_page---byline--8291746220be--------------------------------)[![Shaw Talebi](../Images/1449cc7c08890e2078f9e5d07897e3df.png)](https://shawhin.medium.com/?source=post_page---byline--8291746220be--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8291746220be--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8291746220be--------------------------------) [Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--8291746220be--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8291746220be--------------------------------) ·11分钟阅读·2024年3月27日

--

本文是关于实践中使用大型语言模型（LLMs）的一部分 [系列文章](https://shawhin.medium.com/list/large-language-models-llms-8e009ae3054c)。在 [上一篇文章](/how-to-improve-llms-with-rag-abdc132f76ac)，我们看到如何通过检索增强生成（即 RAG）来改进 LLM。RAG 的一个关键部分是使用文本嵌入从知识库中自动检索相关信息。在这里，我将更深入地讨论文本嵌入，并分享两个简单（但强大的）应用：文本分类和语义搜索。

![](../Images/f5a3647023ea1f969f62faac5595e29a.png)

图片由 [Daniel Lerman](https://unsplash.com/@dlerman6?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

ChatGPT 激发了全球对人工智能及其潜力的想象力。其重要推动力之一是 ChatGPT 的聊天界面，使得人工智能的强大功能变得前所未有地易于接触。

尽管这开启了人工智能热潮和认知的新层次，但围绕“聊天机器人范式”的所有兴奋却使得另一个关键创新（在很大程度上）被忽视。

**LLMs 带来了** **文本嵌入领域的重大创新**。在这里，我将解释这些创新以及如何利用它们来应对简单但高价值的用例。
