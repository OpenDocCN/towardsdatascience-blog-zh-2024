# 内存高效嵌入

> 原文：[https://towardsdatascience.com/memory-efficient-embeddings-d637cba7f006?source=collection_archive---------2-----------------------#2024-01-01](https://towardsdatascience.com/memory-efficient-embeddings-d637cba7f006?source=collection_archive---------2-----------------------#2024-01-01)

## 使用一种新的嵌入层创建更小的模型

[](https://dr-robert-kuebler.medium.com/?source=post_page---byline--d637cba7f006--------------------------------)[![Dr. Robert Kübler](../Images/3b8d8b88f76c0c43d9c305e3885e7ab9.png)](https://dr-robert-kuebler.medium.com/?source=post_page---byline--d637cba7f006--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d637cba7f006--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d637cba7f006--------------------------------) [Dr. Robert Kübler](https://dr-robert-kuebler.medium.com/?source=post_page---byline--d637cba7f006--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d637cba7f006--------------------------------) ·13分钟阅读·2024年1月1日

--

![](../Images/be9ce0d107f6dfeafcb4558c8006314d.png)

图片来源：[Kostiantyn Vierkieiev](https://unsplash.com/@kostiantynvierkieiev?utm_source=medium&utm_medium=referral)来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

每当处理分类数据时，初学者通常会使用**独热编码**。这种方法通常可以，但如果你面对的是成千上万甚至数百万个类别，这种方法就变得**不可行**。其原因如下：

1.  **维度增加：** 对于每个类别，你会得到一个额外的特征。这可能导致*维度灾难*。数据变得更加稀疏，模型可能会面临计算复杂度增加和泛化性能下降的问题。

1.  **语义丧失：** 独热编码将每个类别视为独立的特征，忽略了类别之间潜在的语义关系。我们失去了原始类别变量中存在的有意义的关系。

这些问题出现在自然语言处理领域（我们有一堆词语）或推荐系统（我们有大量的客户和/或文章），可以通过**嵌入**技术来克服。然而，如果你有大量的嵌入，那么模型的内存需求可能会飙升，达到几个GB。

在这篇文章中，我想向你展示几种减少内存占用的方法。其中一种方法来自一篇有趣的论文[组合嵌入](https://arxiv.org/abs/1909.02107)…
