# 如何从任何文档中生成 LLM 微调的指令数据集

> 原文：[`towardsdatascience.com/how-to-generate-instruction-datasets-from-any-documents-for-llm-fine-tuning-abb319a05d91?source=collection_archive---------0-----------------------#2024-03-06`](https://towardsdatascience.com/how-to-generate-instruction-datasets-from-any-documents-for-llm-fine-tuning-abb319a05d91?source=collection_archive---------0-----------------------#2024-03-06)

## 使用轻量级库经济高效地生成高质量的合成数据集

[](https://medium.com/@yanli.liu?source=post_page---byline--abb319a05d91--------------------------------)![Yanli Liu](https://medium.com/@yanli.liu?source=post_page---byline--abb319a05d91--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--abb319a05d91--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--abb319a05d91--------------------------------) [Yanli Liu](https://medium.com/@yanli.liu?source=post_page---byline--abb319a05d91--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--abb319a05d91--------------------------------) ·阅读时间 7 分钟·2024 年 3 月 6 日

--

大语言模型（LLM）是功能强大且通用的工具，但它们通常缺乏特定领域的知识，而这些知识常常储存在企业的仓库中。

使用自己的数据进行定制化 LLM（大语言模型）微调可以弥补这一差距，而数据准备是这一过程的第一步。这也是一个至关重要的步骤，能够显著影响微调模型的表现。

然而，手动创建数据集可能是昂贵且耗时的。另一种方法是利用 LLM 生成合成数据集，通常使用高性能模型如 GPT-4，但这可能会非常昂贵。

在本文中，我旨在向您介绍一种成本效益高的替代方案，用于自动化从各种文档中创建指令数据集。这个解决方案涉及使用一个名为 Bonito 的轻量级开源库。

![](img/3a550f1c96686c676fda82d9b9c908ab.png)

图像由作者使用 Bing 聊天生成，Bing 聊天由 DALL.E 3 驱动

# 开始使用 Bonito，开源解决方案

## 理解指令
