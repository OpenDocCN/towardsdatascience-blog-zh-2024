# RAG 101：分块策略

> 原文：[`towardsdatascience.com/rag-101-chunking-strategies-fdc6f6c2aaec?source=collection_archive---------1-----------------------#2024-10-05`](https://towardsdatascience.com/rag-101-chunking-strategies-fdc6f6c2aaec?source=collection_archive---------1-----------------------#2024-10-05)

## 解锁你 RAG 工作流的全部潜力

## 为什么、何时以及如何对数据进行分块以增强 RAG

[](https://memsranga.com/?source=post_page---byline--fdc6f6c2aaec--------------------------------)![Shanmukha Ranganath](https://memsranga.com/?source=post_page---byline--fdc6f6c2aaec--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fdc6f6c2aaec--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fdc6f6c2aaec--------------------------------) [Shanmukha Ranganath](https://memsranga.com/?source=post_page---byline--fdc6f6c2aaec--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fdc6f6c2aaec--------------------------------) ·12 分钟阅读·2024 年 10 月 5 日

--

![](img/393f81ef2eda31b1e0e61b987efc5154.png)

我们如何将数据分块？（使用 Cava 生成）

大型语言模型在单次请求中能够处理的最大令牌数被称为上下文长度（或上下文窗口）。下表显示了[所有版本的 GPT-4 的上下文长度](https://platform.openai.com/docs/models/gpt-4-turbo-and-gpt-4)（截至 2024 年 9 月）。虽然随着每次迭代和每个新模型的发布，上下文长度在不断增加，但我们仍然面临向模型提供信息的限制。此外，输入大小与由 LLM 生成的响应的上下文相关性之间存在反向关系，简短而集中的输入比包含大量信息的长上下文产生更好的结果。这突显了将数据分解为更小、更相关的块的重要性，以确保从 LLM 获得更合适的响应——至少在 LLM 能够处理大量数据而无需重新训练之前。

图中表示的上下文窗口是**包括输入和输出令牌的。**
