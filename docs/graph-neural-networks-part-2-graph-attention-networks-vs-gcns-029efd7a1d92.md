# 图神经网络 第二部分：图注意力网络与图卷积网络

> 原文：[https://towardsdatascience.com/graph-neural-networks-part-2-graph-attention-networks-vs-gcns-029efd7a1d92?source=collection_archive---------3-----------------------#2024-10-08](https://towardsdatascience.com/graph-neural-networks-part-2-graph-attention-networks-vs-gcns-029efd7a1d92?source=collection_archive---------3-----------------------#2024-10-08)

![](../Images/416f94719e2ed6517b37cc618d4bfa4c.png)

图片由作者使用Dall·E创作。

## 一种关注你图的模型

[](https://hennie-de-harder.medium.com/?source=post_page---byline--029efd7a1d92--------------------------------)[![Hennie de Harder](../Images/20a1be0ed2ac4b535397973ad6148e6b.png)](https://hennie-de-harder.medium.com/?source=post_page---byline--029efd7a1d92--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--029efd7a1d92--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--029efd7a1d92--------------------------------) [Hennie de Harder](https://hennie-de-harder.medium.com/?source=post_page---byline--029efd7a1d92--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--029efd7a1d92--------------------------------) ·阅读时间：8分钟·2024年10月8日

--

**欢迎来到第二篇关于图神经网络（GNN）架构的文章！在上一篇文章中，我们通过使用图卷积网络（GCN）将图结构引入模型，取得了Cora数据集上的准确率惊人提升。本文将解释图注意力网络（GATs），图神经网络的另一种基础架构。我们能否通过GAT进一步提高准确率呢？**

首先，让我们讨论一下GAT和GCN之间的区别。然后，我们将训练一个GAT，并将其准确率与GCN和基本神经网络进行比较。

> 这篇博客文章是系列文章的一部分。如果你是GNN的新手，推荐从[第一篇文章](https://medium.com/towards-data-science/graph-neural-networks-part-1-graph-convolutional-networks-explained-9c6aaa8a406e)开始阅读，文章中讲解了图、神经网络、数据集以及GCN。

# 图注意力网络

在上一篇文章中，我们看到了GCN的实际应用。接下来让我们更进一步，探讨图注意力网络（GATs）。正如你可能记得的那样，GCN对所有邻居的处理是相同的。而GAT则有所不同。GAT允许模型为不同的邻居学习不同的重要性（注意力）得分。它们通过使用注意力机制来聚合邻居的信息（这可能会让你想起……）
