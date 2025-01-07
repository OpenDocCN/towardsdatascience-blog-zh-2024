# 时间序列对大型语言模型（LLMs）来说并没有那么不同

> 原文：[https://towardsdatascience.com/time-series-are-not-that-different-for-llms-56435dc7d2b1?source=collection_archive---------2-----------------------#2024-07-12](https://towardsdatascience.com/time-series-are-not-that-different-for-llms-56435dc7d2b1?source=collection_archive---------2-----------------------#2024-07-12)

## 利用LLMs的力量进行时间序列建模

[](https://medium.com/@a0987284901?source=post_page---byline--56435dc7d2b1--------------------------------)[![H. L](../Images/eaa1b4eb6f6cebc131f4cf0cfdd4cda7.png)](https://medium.com/@a0987284901?source=post_page---byline--56435dc7d2b1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--56435dc7d2b1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--56435dc7d2b1--------------------------------) [H. L](https://medium.com/@a0987284901?source=post_page---byline--56435dc7d2b1--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--56435dc7d2b1--------------------------------) ·阅读时间：7分钟·2024年7月12日

--

基础模型推动了[计算语言学](/what-are-the-data-centric-ai-concepts-behind-gpt-models-a590071bb727)和[计算机视觉](https://medium.com/towards-data-science/the-data-centric-ai-concepts-in-segment-anything-8eea556ac9d)领域的最新进展，并在人工智能（AI）中取得了巨大的成功。成功的基础模型的关键思想包括：

1.  巨大的数据规模：广泛而多样化的训练数据涵盖了一个全面的分布，使得模型能够逼近任何潜在的测试分布。

1.  可迁移性：通过记忆和回忆已学信息的机制，如提示[1]和自监督预训练[2]，使得模型能够有效适应新任务。

![](../Images/0ba1ef6524001b57764d88b30e4b8a70.png)

时间序列基础模型的开发在LLM成功之后变得更加密集。图片来自论文[https://arxiv.org/pdf/2403.14735](https://arxiv.org/pdf/2403.14735)。

## **大型时间序列基础模型（LTSM）**

在基础模型在计算语言学领域取得成功后，越来越多的研究工作正在努力将这种成功复制到另一种类型的序列数据：时间序列。

与大型语言模型（LLMs）类似，大型时间序列基础模型（**LTSM**）旨在从大量多样的时间序列数据中学习，以进行预测。经过训练的基础模型可以针对各种任务进行微调，例如异常检测或时间序列分类。
