# 正确地校准分类概率

> 原文：[https://towardsdatascience.com/calibrating-classification-probabilities-the-right-way-da935caee18d?source=collection_archive---------0-----------------------#2024-09-18](https://towardsdatascience.com/calibrating-classification-probabilities-the-right-way-da935caee18d?source=collection_archive---------0-----------------------#2024-09-18)

## 或者为什么你不应该信任`predict_proba`方法

[](https://medium.com/@jodancker?source=post_page---byline--da935caee18d--------------------------------)[![Jonte Dancker](../Images/29e37a1a1cabc15cfb90a860b2931f03.png)](https://medium.com/@jodancker?source=post_page---byline--da935caee18d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--da935caee18d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--da935caee18d--------------------------------) [Jonte Dancker](https://medium.com/@jodancker?source=post_page---byline--da935caee18d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--da935caee18d--------------------------------) ·阅读时间：8分钟·2024年9月18日

--

![](../Images/728cfc9bbdc5554cd2d3e758d88f6994.png)

Venn-Abers预测器及其二分类器输出（图片由作者提供）。

在之前的文章中，我指出了了解模型对其预测有多确定的重要性。

[](/uncertainty-quantification-and-why-you-should-care-3f8a651f1956?source=post_page-----da935caee18d--------------------------------) [## 不确定性量化及其重要性

### 如何通过三行代码提升你的机器学习模型

[Towards Data Science](https://towardsdatascience.com/uncertainty-quantification-and-why-you-should-care-3f8a651f1956?source=post_page-----da935caee18d--------------------------------)

对于分类问题，仅仅知道最终的类别是没有帮助的。我们需要更多的信息来在后续的过程中做出明智的决策。一个仅输出最终类别的分类模型掩盖了重要信息。我们不知道模型有多确定，以及我们能多大程度上信任它的预测。

我们如何才能更有信心地信任模型？

有两种方法可以帮助我们深入了解分类问题。

我们可以将点预测转换为预测集。预测集的目标是保证它以给定的概率包含真实类别。预测集的大小可以告诉我们模型对其预测的信心水平。预测集包含的类别越少，模型就越确定。
