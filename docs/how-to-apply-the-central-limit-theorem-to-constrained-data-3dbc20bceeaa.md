# 如何将中心极限定理应用于约束数据

> 原文：[https://towardsdatascience.com/how-to-apply-the-central-limit-theorem-to-constrained-data-3dbc20bceeaa?source=collection_archive---------5-----------------------#2024-12-10](https://towardsdatascience.com/how-to-apply-the-central-limit-theorem-to-constrained-data-3dbc20bceeaa?source=collection_archive---------5-----------------------#2024-12-10)

## 我们可以如何描述在区间[a, b]内分布的数据的均值？

[](https://medium.com/@ryan.burn?source=post_page---byline--3dbc20bceeaa--------------------------------)[![Ryan Burn](../Images/5d45aa3c86bbcd5f30e92f2cb6a484f6.png)](https://medium.com/@ryan.burn?source=post_page---byline--3dbc20bceeaa--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3dbc20bceeaa--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3dbc20bceeaa--------------------------------) [Ryan Burn](https://medium.com/@ryan.burn?source=post_page---byline--3dbc20bceeaa--------------------------------)

·发表于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--3dbc20bceeaa--------------------------------) ·14分钟阅读·2024年12月10日

--

假设我们正在衡量一位不受欢迎的政治家的支持率。假设我们随机抽取了十次民意调查，并得到以下结果：

我们如何构造关于该政治家支持率均值的后验分布？

假设这些民意调查是独立且同分布的随机变量，X_1, …, X_n。中心极限定理告诉我们，样本均值将渐近地趋近于一个正态分布，方差为σ²/n。

其中μ和σ²分别是X_i的均值和方差。

![](../Images/d155f44a0cc7da99e3b390a5422cecf1.png)

图1：我们不受欢迎的政治家的样本批准均值的标准化直方图以及正态分布近似图，n=1、n=3、n=5、n=7、n=10和n=20。我们可以看到，当n=10时，样本均值分布已经相当接近其正态分布近似。图由作者提供。

受此渐近极限的启发，我们用以下公式来近似观察数据**y**的似然：

![](../Images/deac40c137457d01c698d524c35f84ef.png)

使用客观先验

（稍后会详细说明），并且通过对σ²的积分，我们得到了后验分布的t分布，π(µ|**y**)

其中

让我们来看一下表1中数据的后验分布。
