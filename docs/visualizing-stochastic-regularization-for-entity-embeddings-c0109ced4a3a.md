# 可视化实体嵌入的随机正则化

> 原文：[https://towardsdatascience.com/visualizing-stochastic-regularization-for-entity-embeddings-c0109ced4a3a?source=collection_archive---------8-----------------------#2024-08-06](https://towardsdatascience.com/visualizing-stochastic-regularization-for-entity-embeddings-c0109ced4a3a?source=collection_archive---------8-----------------------#2024-08-06)

## 揭示神经网络如何感知类别数据及其层次结构

[](https://medium.com/@vla6?source=post_page---byline--c0109ced4a3a--------------------------------)[![Valerie Carey](../Images/9ef394fe5a6a5439521c1905e0195751.png)](https://medium.com/@vla6?source=post_page---byline--c0109ced4a3a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c0109ced4a3a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c0109ced4a3a--------------------------------) [Valerie Carey](https://medium.com/@vla6?source=post_page---byline--c0109ced4a3a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c0109ced4a3a--------------------------------) ·11分钟阅读·2024年8月6日

--

![](../Images/3b2502e5244064b351537fa99c7cff97.png)

图片由 [Rachael Crowe](https://unsplash.com/@hellorachaelcrowe?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

行业数据通常包含许多可能值的非数值数据，例如邮政编码、医疗诊断代码、首选鞋履品牌。这些**高基数类别特征**包含有用信息，但将它们融入机器学习模型中是一门艺术。

我一直在写一系列关于这些特征方法的博客文章。在上一期中，我展示了如何通过扰动训练数据（**随机正则化**）在神经网络模型中显著减少过拟合并提高对未见类别编码的性能 [1]。

> 实际上，当使用层次信息与随机正则化结合时，模型对未见编码的性能可以接近已知编码的表现！

在这里，我使用可视化和SHAP值来“探究内部机制”，并深入了解实体嵌入如何响应随机正则化。这些图像很漂亮，看着数据变化时图表的变化也很酷。此外，这些可视化能够为模型改进提供建议，并能够识别出分析师可能感兴趣的群体。

## NAICS代码
