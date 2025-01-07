# 分析表格数据集，只需要统计学

> 原文：[`towardsdatascience.com/all-you-need-is-statistics-to-analyze-tabular-datasets-3a1717f92749?source=collection_archive---------6-----------------------#2024-09-10`](https://towardsdatascience.com/all-you-need-is-statistics-to-analyze-tabular-datasets-3a1717f92749?source=collection_archive---------6-----------------------#2024-09-10)

## 分析表格数据集不需要深度学习或大型语言模型。我将演示如何通过（简单的）统计方法和像 PCA 这样的技术来揭示新的见解并提供可解释的结果。

[](https://erdogant.medium.com/?source=post_page---byline--3a1717f92749--------------------------------)![Erdogan Taskesen](https://erdogant.medium.com/?source=post_page---byline--3a1717f92749--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3a1717f92749--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3a1717f92749--------------------------------) [Erdogan Taskesen](https://erdogant.medium.com/?source=post_page---byline--3a1717f92749--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3a1717f92749--------------------------------) ·15 分钟阅读·2024 年 9 月 10 日

--

![](img/a0b6726bd432e0d28e13921fe29e8a79.png)

照片由[Dan Cristian Pădureț](https://unsplash.com/@dancristianpaduret?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/photos/orange-and-black-clouds-illustration-hguxpsaUpBk?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

表格数据集是最常见的数据形式之一，包含二进制、类别、文本和连续值等多种变量。例如，著名的表格数据集有泰坦尼克号数据集。在这类数据集中，主要的挑战是如何分析这些变量，因为类别值的分析需要与连续值不同的统计方法和/或模型，等等。此外，关键还在于确定数据集中的多重共线性，因为具有统计相似行为的变量可能会影响模型的可靠性。*在这篇博客文章中，我将展示处理表格数据集的步骤，并展示如何通过统计检验，例如超几何检验，来揭示变量之间的关系。此外，我还将解释多重检验校正的重要性，并展示如何在表格数据集上应用主成分分析（PCA）*。

# 第一步是进行可视化检查。
