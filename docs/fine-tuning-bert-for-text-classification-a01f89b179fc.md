# 微调 BERT 进行文本分类

> 原文：[`towardsdatascience.com/fine-tuning-bert-for-text-classification-a01f89b179fc?source=collection_archive---------1-----------------------#2024-10-17`](https://towardsdatascience.com/fine-tuning-bert-for-text-classification-a01f89b179fc?source=collection_archive---------1-----------------------#2024-10-17)

## 一个可以修改的示例，带有 Python 代码

[](https://shawhin.medium.com/?source=post_page---byline--a01f89b179fc--------------------------------)![Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--a01f89b179fc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a01f89b179fc--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a01f89b179fc--------------------------------) [Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--a01f89b179fc--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a01f89b179fc--------------------------------) ·6 分钟阅读·2024 年 10 月 17 日

--

尽管今天的 100B+参数变换器模型在人工智能领域处于最前沿，但我们仍然可以通过较小的（<1B 参数）模型取得很大进展。在本文中，我将通过一个例子，演示如何微调 BERT（110M 参数）来分类钓鱼 URL。我将首先介绍一些关键概念，然后分享示例 Python 代码。

![](img/7c8e635ce4bf650d83e3b25350eac527.png)

图片来自 Canva。

# **微调**

**微调** **涉及** **通过额外的训练将预训练模型调整到特定的使用案例**。

预训练模型通过无监督学习开发，这避免了对大规模标签数据集的需求。与从头开始训练相比，微调后的模型可以利用预训练模型的表示，显著**降低训练成本**并**提高模型性能**[1]。
