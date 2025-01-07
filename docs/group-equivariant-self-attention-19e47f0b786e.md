# 群体等变自注意力

> 原文：[https://towardsdatascience.com/group-equivariant-self-attention-19e47f0b786e?source=collection_archive---------12-----------------------#2024-01-09](https://towardsdatascience.com/group-equivariant-self-attention-19e47f0b786e?source=collection_archive---------12-----------------------#2024-01-09)

## 将几何先验注入 Transformer 模型

[](https://medium.com/@ju2ez?source=post_page---byline--19e47f0b786e--------------------------------)[![Julian Hatzky](../Images/9f1ce9a29d215feeb5223e8fd659383e.png)](https://medium.com/@ju2ez?source=post_page---byline--19e47f0b786e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--19e47f0b786e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--19e47f0b786e--------------------------------) [Julian Hatzky](https://medium.com/@ju2ez?source=post_page---byline--19e47f0b786e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--19e47f0b786e--------------------------------) ·阅读时间 7 分钟·2024年1月9日

--

在不断发展的神经架构的动态环境中，效率至关重要。为特定任务定制网络涉及注入*先验*知识，这可以通过战略性地调整架构来实现。这不仅仅是调整参数——更重要的是将所需的理解嵌入到模型中。实现这一目标的一种方法是使用几何先验——这正是本文要讨论的话题。

![](../Images/2f5ed3dba791818cb5322f689cd8f3a7.png)

一张狗在 90 度旋转下的照片。中间是具有旋转等变性的模型的权重表示，而右侧的模型则没有这种等变性。©J. Hatzky

## 前提条件

[在上一篇文章中](https://medium.com/towards-data-science/towards-stand-alone-self-attention-in-vision-3d0561c6aee5)我们深入探讨了视觉中的自注意力操作。

现在让我们在此基础上进行拓展，利用几何深度学习的最新进展。

如果你还不熟悉几何深度学习，[Michael Bronstein 创建了一个很好的入门系列](/towards-geometric-deep-learning-i-on-the-shoulders-of-giants-726c205860f5)。

## 群体等变模型的优势

等变模型可以根据当前任务定制搜索空间，并降低模型学习到虚假关系的概率。

![](../Images/2bb0088e601c32f58df2f8b796ad058d.png)

癌细胞在 90 度旋转下的表现。中间是具有旋转等变性的模型的权重表示，而右侧的模型则没有这种等变性。©J. Hatzky
