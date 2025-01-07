# 微调BERT进行文本分类

> 原文：[https://towardsdatascience.com/fine-tuning-bert-for-text-classification-a01f89b179fc?source=collection_archive---------1-----------------------#2024-10-17](https://towardsdatascience.com/fine-tuning-bert-for-text-classification-a01f89b179fc?source=collection_archive---------1-----------------------#2024-10-17)

## 一个可以修改的示例，带有Python代码

[](https://shawhin.medium.com/?source=post_page---byline--a01f89b179fc--------------------------------)[![Shaw Talebi](../Images/1449cc7c08890e2078f9e5d07897e3df.png)](https://shawhin.medium.com/?source=post_page---byline--a01f89b179fc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a01f89b179fc--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a01f89b179fc--------------------------------) [Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--a01f89b179fc--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a01f89b179fc--------------------------------) ·6分钟阅读·2024年10月17日

--

尽管今天的100B+参数变换器模型在人工智能领域处于最前沿，但我们仍然可以通过较小的（<1B参数）模型取得很大进展。在本文中，我将通过一个例子，演示如何微调BERT（110M参数）来分类钓鱼URL。我将首先介绍一些关键概念，然后分享示例Python代码。

![](../Images/7c8e635ce4bf650d83e3b25350eac527.png)

图片来自Canva。

# **微调**

[**微调**](/fine-tuning-large-language-models-llms-23473d763b91) **涉及** **通过额外的训练将预训练模型调整到特定的使用案例**。

预训练模型通过无监督学习开发，这避免了对大规模标签数据集的需求。与从头开始训练相比，微调后的模型可以利用预训练模型的表示，显著**降低训练成本**并**提高模型性能**[1]。
