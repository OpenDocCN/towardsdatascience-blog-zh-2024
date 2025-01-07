# 如何在 RAGs 中实现接近人类水平的分块效果

> 原文：[`towardsdatascience.com/agentic-chunking-for-rags-091beccd94b1?source=collection_archive---------0-----------------------#2024-08-26`](https://towardsdatascience.com/agentic-chunking-for-rags-091beccd94b1?source=collection_archive---------0-----------------------#2024-08-26)

## 成本较高但强大的分块技术，有助于提升 RAG 的检索效果。

[](https://thuwarakesh.medium.com/?source=post_page---byline--091beccd94b1--------------------------------)![Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--091beccd94b1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--091beccd94b1--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--091beccd94b1--------------------------------) [Thuwarakesh Murallie](https://thuwarakesh.medium.com/?source=post_page---byline--091beccd94b1--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--091beccd94b1--------------------------------) ·8 分钟阅读·2024 年 8 月 26 日

--

![](img/82aba3e7f7d96c5ab42e2324773c1286.png)

图片由[娜塔莉娅·维特凯维奇](https://www.pexels.com/photo/a-person-cutting-melon-4772957/)提供。

好的分块能带来好的 RAG。

分块、嵌入和索引是 RAGs（检索增强生成模型）的关键方面。使用合适的分块技术的 RAG 应用在输出质量和速度方面表现优异。

在构建 LLM（大语言模型）管道时，我们使用不同的策略来拆分文本。递归字符拆分是最流行的技术。它采用滑动窗口方法，窗口长度固定。然而，这种方法不能保证足够地容纳一个主题在其窗口大小内。此外，还有可能部分上下文会被分配到不同的分块中。

我喜欢的另一种技术是语义分割。语义分割在两个连续句子之间有显著变化时进行拆分。它没有长度限制，因此可以有许多句子，也可以只有很少的句子。但它更有可能准确捕捉到不同的主题。

即便是语义分割方法也存在问题。

> 如果彼此相距较远的句子在意义上更为接近，应该怎么处理呢？
