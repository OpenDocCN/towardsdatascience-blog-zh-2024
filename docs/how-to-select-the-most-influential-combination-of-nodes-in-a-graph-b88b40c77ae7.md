# 如何选择图中最具影响力的节点组合

> 原文：[`towardsdatascience.com/how-to-select-the-most-influential-combination-of-nodes-in-a-graph-b88b40c77ae7?source=collection_archive---------7-----------------------#2024-03-13`](https://towardsdatascience.com/how-to-select-the-most-influential-combination-of-nodes-in-a-graph-b88b40c77ae7?source=collection_archive---------7-----------------------#2024-03-13)

## 本文讨论了如何选择一组对图具有最大综合影响力的节点。

[](https://oieivind.medium.com/?source=post_page---byline--b88b40c77ae7--------------------------------)![Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--b88b40c77ae7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b88b40c77ae7--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b88b40c77ae7--------------------------------) [Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--b88b40c77ae7--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b88b40c77ae7--------------------------------) ·16 分钟阅读·2024 年 3 月 13 日

--

在寻找图中的影响力节点时，您可以考虑一些图度量，如中心性或度数，这些指标可以告诉你单个节点的影响力有多大。然而，要找到图中最具影响力的节点集，您必须考虑哪种节点组合对图具有最高影响力，这个问题是非常具有挑战性的。本文探讨了如何解决从图中选择最具影响力节点集的问题。

![](img/914b7cf2244c5c26d25cf193078d0dfb.png)

通过本文了解如何选择图中最具影响力的节点组合。图片由 ChatGPT 提供。 “制作一张关于‘选择图中最具影响力节点组合’的图片”提示。*ChatGPT*，4，OpenAI，2024 年 3 月 10 日。 [`chat.openai.com.`](https://chat.openai.com.)

# 动机

我写这篇文章的动机是因为我目前正在进行我的论文研究，内容涉及半监督聚类。实际上，我需要从图中选择一些可以知道标签的节点，然后利用这些信息对其他节点进行聚类。因此，找到最具影响力的节点集以便了解其标签对于我的聚类算法的性能至关重要。在本文的情况下，图的影响力将被看作是所选节点集在多大程度上能帮助聚类算法。然而，这种对图的影响力也可以推广到其他问题中。
