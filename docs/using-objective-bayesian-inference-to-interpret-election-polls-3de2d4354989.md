# 使用客观贝叶斯推断解读选举民意调查

> 原文：[https://towardsdatascience.com/using-objective-bayesian-inference-to-interpret-election-polls-3de2d4354989?source=collection_archive---------8-----------------------#2024-10-30](https://towardsdatascience.com/using-objective-bayesian-inference-to-interpret-election-polls-3de2d4354989?source=collection_archive---------8-----------------------#2024-10-30)

## 如何构建一个仅基于民意调查的客观贝叶斯模型，从州的民意调查领先转化为赢得该州的概率。

[](https://medium.com/@ryan.burn?source=post_page---byline--3de2d4354989--------------------------------)[![Ryan Burn](../Images/5d45aa3c86bbcd5f30e92f2cb6a484f6.png)](https://medium.com/@ryan.burn?source=post_page---byline--3de2d4354989--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3de2d4354989--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3de2d4354989--------------------------------) [Ryan Burn](https://medium.com/@ryan.burn?source=post_page---byline--3de2d4354989--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3de2d4354989--------------------------------) ·7分钟阅读·2024年10月30日

--

随着总统选举临近，我和许多其他人都有一个问题：候选人在某州的民意调查结果是否能够反映出他们赢得该州的概率。

在这篇博客文章中，我想通过使用客观贝叶斯推断（[3]）和2016年及2020年的选举结果来探讨这个问题。目标是构建一个简单的仅基于民意调查的模型，该模型以候选人在各州的民意调查领先为基础，生成候选人赢得该州的后验概率分布。

![](../Images/e53dc13498959c6a7b2cab3103f928d4.png)

图1：使用2016年和2020年的FiveThirtyEight民意调查数据（[1, 2]）以及宾夕法尼亚州的民意调查快照，预测的赢得概率的后验分布示例。图中还展示了预测后验分布的5百分位、50百分位和95百分位。图源自作者。

其中，后验分布衡量我们对民意调查预测准确度的信任程度。

对于该模型，我将使用逻辑回归，并且包含一个未知权重变量w：

以2020年和2016年的选举结果为观察数据，并使用适当的先验分布π，我们可以生成一个未知权重的后验分布。

其中
