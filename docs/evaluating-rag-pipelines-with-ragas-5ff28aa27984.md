# 使用Ragas评估RAG管道

> 原文：[https://towardsdatascience.com/evaluating-rag-pipelines-with-ragas-5ff28aa27984?source=collection_archive---------2-----------------------#2024-06-30](https://towardsdatascience.com/evaluating-rag-pipelines-with-ragas-5ff28aa27984?source=collection_archive---------2-----------------------#2024-06-30)

## 利用Ragas框架来确定你的检索增强生成（RAG）管道的性能

[](https://dkhundley.medium.com/?source=post_page---byline--5ff28aa27984--------------------------------)[![David Hundley](../Images/1779ef96ec3d338f8fe4a9567ba7b194.png)](https://dkhundley.medium.com/?source=post_page---byline--5ff28aa27984--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5ff28aa27984--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5ff28aa27984--------------------------------) [David Hundley](https://dkhundley.medium.com/?source=post_page---byline--5ff28aa27984--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5ff28aa27984--------------------------------) ·21分钟阅读·2024年6月30日

--

![](../Images/e86382229b5a65e62cc3f56f8a7eeb89.png)

由作者创建的标题卡

人工智能确实很酷，但无论好坏，所有AI模型的输出都是推断。换句话说，这些输出是经过教育的猜测，我们永远无法真正确定输出是否正确。在传统的机器学习背景下，我们通常可以计算如ROC AUC、RMSE等指标，以确保模型随时间推移保持性能。

不幸的是，在深度学习的背景下（包括大型语言模型的输出）并没有像前面提到的那样的数学度量指标。更具体地说，我们可能会对如何评估**检索增强生成（RAG）用例的有效性**感兴趣。鉴于我们无法应用一些典型的数学公式来推导度量标准，那么这给我们留下了哪些选择？

总是可以使用的第一个选项是人工评估。虽然这无疑是一条有效的路径，但它既不高效，也不总是最可靠的。首先，使用人工评估者的挑战在于他们带有自己的偏见，这意味着你不能指望一个评估者与另一个评估者保持一致。此外，它还可能…
