# 不让任何标签被遗漏：层次化类别的替代编码方法

> 原文：[`towardsdatascience.com/no-label-left-behind-alternative-encodings-for-hierarchical-categoricals-d1bcf00afc37?source=collection_archive---------8-----------------------#2024-05-17`](https://towardsdatascience.com/no-label-left-behind-alternative-encodings-for-hierarchical-categoricals-d1bcf00afc37?source=collection_archive---------8-----------------------#2024-05-17)

## 寻找一个适用于当前和未来编码的系统

[](https://medium.com/@vla6?source=post_page---byline--d1bcf00afc37--------------------------------)![Valerie Carey](https://medium.com/@vla6?source=post_page---byline--d1bcf00afc37--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d1bcf00afc37--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d1bcf00afc37--------------------------------) [Valerie Carey](https://medium.com/@vla6?source=post_page---byline--d1bcf00afc37--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d1bcf00afc37--------------------------------) ·阅读时间 15 分钟·2024 年 5 月 17 日

--

![](img/e8475894dbdfff90650116eaf11a181c.png)

图片来源：[Gabriel Tenan](https://unsplash.com/@tenans_?utm_source=medium&utm_medium=referral) 通过 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

作为数据科学家的我，经常会遇到很多标签。数据中包含了邮政编码标签、性别标签、医疗诊断标签、职位标签、股票代码标签，等等。标签可以是简单的（如 S、M、L 尺码的衬衫）或复杂的（国际疾病分类系统编码了超过 70,000 种医疗状况，这些编码有时可能**非常具体**，[令人忍俊不禁](https://etactics.com/blog/funny-icd-10-codes)）.

当它们出现在数据中时，我们称这些标签为**类别特征**。具有“很多”可能值的类别特征被称为**高基数类别特征**。高基数类别特征在机器学习模型中使用时会带来困难。大量的维度使得它们无法直接使用，或使用起来不切实际（例如“维度灾难”）。因此，采用了各种编码方案来简化这些特征。

**低频**或**未见过**的编码也为高基数类别特征带来了挑战。例如，一些邮政编码的区域人口稀少，而其他邮政编码区域则拥有数百万居民；我们对某些编码的信心更高。此外，常见的编码集，如医疗诊断编码，定期更新，导致在训练时没有可用的未见过值。**未见过和低频**…
