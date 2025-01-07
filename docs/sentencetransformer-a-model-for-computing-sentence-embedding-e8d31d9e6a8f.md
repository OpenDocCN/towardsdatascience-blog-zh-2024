# SentenceTransformer：一种计算句子嵌入的模型

> 原文：[https://towardsdatascience.com/sentencetransformer-a-model-for-computing-sentence-embedding-e8d31d9e6a8f?source=collection_archive---------6-----------------------#2024-01-16](https://towardsdatascience.com/sentencetransformer-a-model-for-computing-sentence-embedding-e8d31d9e6a8f?source=collection_archive---------6-----------------------#2024-01-16)

## 将BERT转换为高效的句子变换器

[](https://medium.com/@mina.ghashami?source=post_page---byline--e8d31d9e6a8f--------------------------------)[![Mina Ghashami](../Images/745f53b94f5667a485299b49913c7a21.png)](https://medium.com/@mina.ghashami?source=post_page---byline--e8d31d9e6a8f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e8d31d9e6a8f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e8d31d9e6a8f--------------------------------) [Mina Ghashami](https://medium.com/@mina.ghashami?source=post_page---byline--e8d31d9e6a8f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e8d31d9e6a8f--------------------------------) ·阅读时间7分钟·2024年1月16日

--

在这篇文章中，我们将探讨SentenceTransformer [1]，该模型于2019年发布。SentenceTransformer采用了双编码器架构，并将BERT模型适配以生成高效的句子嵌入。

BERT（双向编码器表示的变换器）是基于这样一种理念构建的：所有NLP任务都依赖于词元/单词的语义。BERT训练分为两个阶段：1）预训练阶段，在此阶段BERT学习语言的普遍语义；2）微调阶段，在此阶段BERT在特定任务上进行训练。

![](../Images/52937cabeba180d1e79c4be33abdf476.png)

图片来自[[3](https://arxiv.org/pdf/1810.04805.pdf)]

BERT非常擅长学习单词/词元的语义，但它不擅长学习句子的语义。因此，它在某些任务上表现不佳，例如句子分类、句子对之间的相似度计算。

由于BERT生成的是词元嵌入，因此从BERT中获取句子嵌入的一种方式是对所有词元的嵌入取平均值。SentenceTransformer论文[1]表明，这种方式会产生非常低质量的句子嵌入，几乎和使用GLOVE嵌入一样差。这些嵌入不能捕捉句子的语义。
