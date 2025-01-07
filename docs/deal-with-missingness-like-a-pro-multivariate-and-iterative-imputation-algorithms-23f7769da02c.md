# 像专家一样处理缺失数据：多变量和迭代插补算法

> 原文：[https://towardsdatascience.com/deal-with-missingness-like-a-pro-multivariate-and-iterative-imputation-algorithms-23f7769da02c?source=collection_archive---------12-----------------------#2024-12-12](https://towardsdatascience.com/deal-with-missingness-like-a-pro-multivariate-and-iterative-imputation-algorithms-23f7769da02c?source=collection_archive---------12-----------------------#2024-12-12)

## 使用LightGBM、kNN和自编码器进行插补，并通过迭代方法MICE进一步优化

[](https://gizkaya.medium.com/?source=post_page---byline--23f7769da02c--------------------------------)[![Gizem Kaya](../Images/29d29f04df742f8c19cb9fb0c7169e5d.png)](https://gizkaya.medium.com/?source=post_page---byline--23f7769da02c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--23f7769da02c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--23f7769da02c--------------------------------) [Gizem Kaya](https://gizkaya.medium.com/?source=post_page---byline--23f7769da02c--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--23f7769da02c--------------------------------) ·阅读时长15分钟·2024年12月12日

--

现实世界中的数据通常是杂乱无章的，在用于任何机器学习（ML）模型之前需要仔细的预处理。我们几乎总是会在数据集中遇到空值，如果能加以观察，这些值可能对我们的分析或建模非常有价值。我们称之为**数据中的缺失性**。

缺失值可能有多种原因，例如设备故障、ERP系统中的非必填字段，或是调查中对参与者不适用的问题。根据原因的不同，缺失的性质也会有所不同。如何理解这种性质在[我之前的文章](https://medium.com/towards-data-science/addressing-missing-data-f6f7920bcc55)中有详细解释。在本文中，重点主要放在如何正确处理这些缺失值，避免通过删除或插补造成偏差或丧失重要洞察。

***红酒质量*** 数据来自 ***UCI机器学习库***，本文使用了该数据集 [1]。这是一个开源数据集，可以通过此[链接](https://archive.ics.uci.edu/dataset/186/wine+quality)进行下载。

> 理解缺失值的性质（MCAR、MAR、MNAR）对于选择正确的处理方法至关重要。因此，如果您认为需要更多相关信息，建议您首先阅读我之前的[文章](https://medium.com/towards-data-science/addressing-missing-data-f6f7920bcc55)。
