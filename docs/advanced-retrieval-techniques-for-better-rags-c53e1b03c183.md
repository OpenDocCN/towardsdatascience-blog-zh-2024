# 更高级的递归与跟进检索技术，用于提升 RAG 性能

> 原文：[https://towardsdatascience.com/advanced-retrieval-techniques-for-better-rags-c53e1b03c183?source=collection_archive---------5-----------------------#2024-08-12](https://towardsdatascience.com/advanced-retrieval-techniques-for-better-rags-c53e1b03c183?source=collection_archive---------5-----------------------#2024-08-12)

## 破解问题就是解决一半，链式处理让问题解决得更好。

[](https://thuwarakesh.medium.com/?source=post_page---byline--c53e1b03c183--------------------------------)[![Thuwarakesh Murallie](../Images/44f1a14a899426592bbd8c7f73ce169d.png)](https://thuwarakesh.medium.com/?source=post_page---byline--c53e1b03c183--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c53e1b03c183--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c53e1b03c183--------------------------------) [Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--c53e1b03c183--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c53e1b03c183--------------------------------) ·15分钟阅读·2024年8月12日

--

![](../Images/707f137ddeb3c4c0e3e08a9e300045d2.png)

图片由 [Mike Alonzo](https://unsplash.com/@mikezo?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

没有比查询修改更好的方式来提升大型语言模型（LLM）的表现。

在我最近的文章中，我讨论了五种查询翻译技巧以及它们如何改善 RAG 应用中的检索过程。一个技巧是查询分解。

[](/5-proven-query-translation-techniques-to-boost-your-rag-performance-47db12efe971?source=post_page-----c53e1b03c183--------------------------------) [## 提升 RAG 性能的五种经过验证的查询翻译技巧

### 如何在用户输入含糊不清的情况下获得近乎完美的 LLM 性能

[towardsdatascience.com](/5-proven-query-translation-techniques-to-boost-your-rag-performance-47db12efe971?source=post_page-----c53e1b03c183--------------------------------)

这个极好的技巧通过创建子问题来构建一个更详细的答案来回应我们最初的查询。这些子问题将被用于检索过程。最终的 LLM 会将每一对问题和答案作为上下文，生成对我们最初查询的全面回答。

本文讨论了另外两种常与查询分解结合使用的提升技巧，以获得更好的效果。

第一个技巧是**递归回答**，它通过大量生成子问题并递归回答它们。第二个技巧是**跟进提问**。正如你可能猜到的那样，我们回答这些问题…
