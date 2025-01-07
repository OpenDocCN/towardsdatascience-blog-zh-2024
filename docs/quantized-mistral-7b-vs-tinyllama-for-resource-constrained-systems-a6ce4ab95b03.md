# 量化的 Mistral 7B 与 TinyLlama 在资源受限系统中的比较

> 原文：[`towardsdatascience.com/quantized-mistral-7b-vs-tinyllama-for-resource-constrained-systems-a6ce4ab95b03?source=collection_archive---------4-----------------------#2024-02-17`](https://towardsdatascience.com/quantized-mistral-7b-vs-tinyllama-for-resource-constrained-systems-a6ce4ab95b03?source=collection_archive---------4-----------------------#2024-02-17)

## 这两种模型在 RAG 问答设置中的准确性和响应时间的性能比较。

[](https://medium.com/@heelara?source=post_page---byline--a6ce4ab95b03--------------------------------)![Kennedy Selvadurai 博士](https://medium.com/@heelara?source=post_page---byline--a6ce4ab95b03--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a6ce4ab95b03--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a6ce4ab95b03--------------------------------) [Kennedy Selvadurai 博士](https://medium.com/@heelara?source=post_page---byline--a6ce4ab95b03--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a6ce4ab95b03--------------------------------) ·阅读时间 8 分钟·2024 年 2 月 17 日

--

![](img/0d8c76deed39329ca0bb4fe416a44d64.png)

由 Canva 生成，作者提供的提示

随着法国初创公司 Mistral 推出开源语言模型 Mistral 7B，像 ChatGPT 和 claude.ai 这样的专有模型所展现的惊人性能也开始向开源社区开放。为了探索在资源受限系统上使用该模型的可行性，其*量化*版本已被证明能够保持出色的性能。

尽管 2 位量化的 Mistral 7B 模型在我们之前的[研究](https://medium.com/@heelara/querying-internal-documents-using-mistral-7b-with-context-of-ensemble-retrievers-f40aadaf8a6e)中通过了准确性测试，但在 Mac 上响应问题的平均时间约为 2 分钟。现在介绍 TinyLlama [1]，一个紧凑的 1.1B 语言模型，预训练于 3 万亿个 token，采用与 Llama 2 相同的架构和分词器，旨在适用于资源受限的环境。

在本文中，我们将比较量化的 Mistral 7B 与量化的 TinyLlama 1.1B 在集成检索增强生成（RAG）设置中的问答能力的准确性和响应时间性能。

**内容**

启用技术

系统架构

环境设置

实现

结果与…
