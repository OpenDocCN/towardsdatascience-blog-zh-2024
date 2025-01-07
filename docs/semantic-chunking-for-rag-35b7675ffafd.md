# 为什么基于位置的分块会导致 RAG 性能差？

> 原文：[https://towardsdatascience.com/semantic-chunking-for-rag-35b7675ffafd?source=collection_archive---------7-----------------------#2024-08-22](https://towardsdatascience.com/semantic-chunking-for-rag-35b7675ffafd?source=collection_archive---------7-----------------------#2024-08-22)

## 如何实现语义分块并获得更好的结果。

[](https://thuwarakesh.medium.com/?source=post_page---byline--35b7675ffafd--------------------------------)[![Thuwarakesh Murallie](../Images/44f1a14a899426592bbd8c7f73ce169d.png)](https://thuwarakesh.medium.com/?source=post_page---byline--35b7675ffafd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--35b7675ffafd--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--35b7675ffafd--------------------------------) [Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--35b7675ffafd--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--35b7675ffafd--------------------------------) ·阅读时间 11 分钟·2024年8月22日

--

![](../Images/cab396b365bca88a9b5329d247790974.png)

图片来自 [vackground.com](https://unsplash.com/@vackground?utm_source=medium&utm_medium=referral) ，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

邻居节点可能仍然不同。

语言模型有上下文限制。对于较新的 OpenAI 模型，这个限制大约为 128k 个标记，约 80k 个英文单词。这对于大多数使用场景来说似乎已经足够大。然而，大型生产级应用程序通常需要引用超过 80k 个单词，更不用说图片、表格和其他非结构化信息了。

即使我们将所有内容填充在上下文窗口中，并加入更多无关信息，LLM 的性能也会显著下降。

这就是 RAG 发挥作用的地方。RAG 从嵌入的源中检索相关信息，并将其作为上下文传递给 LLM。为了检索“相关信息”，我们应当将文档划分为多个块。因此，分块在 RAG 流程中起着至关重要的作用。

分块帮助 RAG 检索大型文档中的特定部分。然而，分块策略的微小变化可能会显著影响 LLM 的响应。
