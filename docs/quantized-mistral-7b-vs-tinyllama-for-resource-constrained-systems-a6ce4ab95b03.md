# 量化的Mistral 7B与TinyLlama在资源受限系统中的比较

> 原文：[https://towardsdatascience.com/quantized-mistral-7b-vs-tinyllama-for-resource-constrained-systems-a6ce4ab95b03?source=collection_archive---------4-----------------------#2024-02-17](https://towardsdatascience.com/quantized-mistral-7b-vs-tinyllama-for-resource-constrained-systems-a6ce4ab95b03?source=collection_archive---------4-----------------------#2024-02-17)

## 这两种模型在RAG问答设置中的准确性和响应时间的性能比较。

[](https://medium.com/@heelara?source=post_page---byline--a6ce4ab95b03--------------------------------)[![Kennedy Selvadurai博士](../Images/8c9175b153ef6a6fdc1793473c506718.png)](https://medium.com/@heelara?source=post_page---byline--a6ce4ab95b03--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a6ce4ab95b03--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a6ce4ab95b03--------------------------------) [Kennedy Selvadurai博士](https://medium.com/@heelara?source=post_page---byline--a6ce4ab95b03--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a6ce4ab95b03--------------------------------) ·阅读时间8分钟·2024年2月17日

--

![](../Images/0d8c76deed39329ca0bb4fe416a44d64.png)

由Canva生成，作者提供的提示

随着法国初创公司Mistral推出开源语言模型Mistral 7B，像ChatGPT和claude.ai这样的专有模型所展现的惊人性能也开始向开源社区开放。为了探索在资源受限系统上使用该模型的可行性，其*量化*版本已被证明能够保持出色的性能。

尽管2位量化的Mistral 7B模型在我们之前的[研究](https://medium.com/@heelara/querying-internal-documents-using-mistral-7b-with-context-of-ensemble-retrievers-f40aadaf8a6e)中通过了准确性测试，但在Mac上响应问题的平均时间约为2分钟。现在介绍TinyLlama [1]，一个紧凑的1.1B语言模型，预训练于3万亿个token，采用与Llama 2相同的架构和分词器，旨在适用于资源受限的环境。

在本文中，我们将比较量化的Mistral 7B与量化的TinyLlama 1.1B在集成检索增强生成（RAG）设置中的问答能力的准确性和响应时间性能。

**内容**

[启用技术](#d434)

[系统架构](#9cab)

[环境设置](#fd36)

[实现](#2e2c)

[结果与](#f541)…
