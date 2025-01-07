# 如何通过提示压缩将RAG成本降低80%

> 原文：[https://towardsdatascience.com/how-to-cut-rag-costs-by-80-using-prompt-compression-877a07c6bedb?source=collection_archive---------0-----------------------#2024-01-04](https://towardsdatascience.com/how-to-cut-rag-costs-by-80-using-prompt-compression-877a07c6bedb?source=collection_archive---------0-----------------------#2024-01-04)

## 通过提示压缩加速推理

[](https://medium.com/@brezeanu.iulia?source=post_page---byline--877a07c6bedb--------------------------------)[![Iulia Brezeanu](../Images/f108eeec620ec9be40778dfaceca4e6c.png)](https://medium.com/@brezeanu.iulia?source=post_page---byline--877a07c6bedb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--877a07c6bedb--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--877a07c6bedb--------------------------------) [Iulia Brezeanu](https://medium.com/@brezeanu.iulia?source=post_page---byline--877a07c6bedb--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--877a07c6bedb--------------------------------) ·阅读时间11分钟·2024年1月4日

--

![](../Images/dc087a93d65e728e01c7bf6895e134c8.png)

图片来源：作者。AI生成。

推理过程是大规模语言模型使用中显著增加金钱和时间成本的因素之一。对于较长的输入，这一问题尤为突出。以下是模型性能与推理时间之间的关系。

![](../Images/66a23213977cf798c55cfa2556c09cb6.png)

性能评分与推理吞吐量[1]

快速模型每秒生成更多的标记，但在Open LLM排行榜中往往得分较低。扩大模型规模可以提高性能，但代价是推理吞吐量降低。这使得在实际应用中部署这些模型变得困难[1]。

提高LLM的速度并减少资源需求将使得它们能被个人或小型组织更广泛使用。

提出了多种提高LLM效率的解决方案；有些专注于模型架构或系统。然而，像ChatGPT或Claude这样的专有模型只能通过API访问，因此我们无法更改其内部算法。

我们将讨论一种简单且廉价的方法，该方法仅依赖于改变输入给模型的提示……
