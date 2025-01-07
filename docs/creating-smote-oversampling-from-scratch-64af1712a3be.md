# 从头创建 SMOTE 过采样

> 原文：[`towardsdatascience.com/creating-smote-oversampling-from-scratch-64af1712a3be?source=collection_archive---------5-----------------------#2024-12-31`](https://towardsdatascience.com/creating-smote-oversampling-from-scratch-64af1712a3be?source=collection_archive---------5-----------------------#2024-12-31)

## 一篇关于如何实现过采样以及如何制作自定义变体的 Python 教程

[](https://hd2zm.medium.com/?source=post_page---byline--64af1712a3be--------------------------------)![Hari Devanathan](https://hd2zm.medium.com/?source=post_page---byline--64af1712a3be--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--64af1712a3be--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--64af1712a3be--------------------------------) [Hari Devanathan](https://hd2zm.medium.com/?source=post_page---byline--64af1712a3be--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--64af1712a3be--------------------------------) ·8 分钟阅读·4 天前

--

![](img/a1d2c373a08a563ff89cb1d4611727be.png)

图片来源：[By Topo](https://unsplash.com/@bytopo?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

合成少数类过采样技术（SMOTE）通常用于处理数据集中的类别不平衡问题。假设有两个类别，其中一个类别的样本数量远多于另一个类别（多数类）。在这种情况下，SMOTE 会在少数类中生成更多的合成样本，使其与多数类平衡。

在现实世界中，我们不会拥有平衡的数据集用于分类问题。例如，一个预测患者是否患有镰状细胞病的分类器。如果患者的血红蛋白水平异常（6-11 g/dL），这通常是镰状细胞病的强预测因素。如果患者的血红蛋白水平正常（12 mg/dL），仅凭这一指标无法判断患者是否患有镰状细胞病。

然而，在美国大约有 100,000 名患者被诊断为镰状细胞病。目前，美国人口为 3.349 亿人。如果我们有一个包含每个美国公民的数据集，并且标注是否患有镰状细胞病，那么有 0.02% 的人群患有该病。我们面临着严重的类别不平衡问题。我们的模型无法提取出有意义的特征来预测这一异常情况。
