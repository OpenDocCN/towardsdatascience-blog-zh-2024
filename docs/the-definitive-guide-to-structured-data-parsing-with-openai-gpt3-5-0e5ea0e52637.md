# 《使用 OpenAI GPT3.5 解析结构化数据的终极指南》

> 原文：[`towardsdatascience.com/the-definitive-guide-to-structured-data-parsing-with-openai-gpt3-5-0e5ea0e52637?source=collection_archive---------9-----------------------#2024-04-16`](https://towardsdatascience.com/the-definitive-guide-to-structured-data-parsing-with-openai-gpt3-5-0e5ea0e52637?source=collection_archive---------9-----------------------#2024-04-16)

## 系统性地比较了 Instructor、Fructose、Mirascope 和 Langchain 在三个复杂的现实世界结构化数据解析任务中的表现。

[](https://stephen-leo.medium.com/?source=post_page---byline--0e5ea0e52637--------------------------------)![Marie Stephen Leo](https://stephen-leo.medium.com/?source=post_page---byline--0e5ea0e52637--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0e5ea0e52637--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0e5ea0e52637--------------------------------) [Marie Stephen Leo](https://stephen-leo.medium.com/?source=post_page---byline--0e5ea0e52637--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0e5ea0e52637--------------------------------) ·阅读时间：8 分钟·2024 年 4 月 16 日

--

![](img/d3a404445478a2d6bebbaf07d3f18b61.png)

图像由作者使用 ChatGPT 生成

从大型语言模型（LLMs）解析结构化数据对于超出简单问题的任务来说可能非常令人沮丧。然而，可靠地将 LLM 输出解析为预定义的结构对于将 LLM 集成到其他软件系统和生成性 AI 应用中至关重要。OpenAI 通过发布 GPT 函数调用（[链接](https://platform.openai.com/docs/guides/function-calling)）和 JSON 模式（[链接](https://platform.openai.com/docs/guides/text-generation/json-mode)）走在了前列。尽管如此，这些方法仍然需要大量的提示工程、强大的解析、重试和优雅的错误处理，才能在生产级现实问题中可靠运行。

以下是我在使用 LLM 解析结构化数据时遇到的一些问题。本文完全由人工撰写，并借助 Grammarly 语法检查工具，Grammarly 一直是我自 2019 年以来的写作方法。

1.  **分类：** LLM（大语言模型）必须严格遵守允许的类别列表，实际问题中类别数量可能多达数十到数百个。在类别超过几个时，LLM 会开始对不允许的类别进行幻觉生成。

1.  **命名实体识别（NER）：** LLM 应仅选择文本中明确出现的实体。这些实体…
