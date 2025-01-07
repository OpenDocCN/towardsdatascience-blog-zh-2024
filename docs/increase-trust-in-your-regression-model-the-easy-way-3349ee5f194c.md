# 轻松提高你回归模型的信任度

> 原文：[https://towardsdatascience.com/increase-trust-in-your-regression-model-the-easy-way-3349ee5f194c?source=collection_archive---------4-----------------------#2024-11-13](https://towardsdatascience.com/increase-trust-in-your-regression-model-the-easy-way-3349ee5f194c?source=collection_archive---------4-----------------------#2024-11-13)

## 如何使用符合化分位回归

[](https://medium.com/@jodancker?source=post_page---byline--3349ee5f194c--------------------------------)[![Jonte Dancker](../Images/29e37a1a1cabc15cfb90a860b2931f03.png)](https://medium.com/@jodancker?source=post_page---byline--3349ee5f194c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3349ee5f194c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3349ee5f194c--------------------------------) [Jonte Dancker](https://medium.com/@jodancker?source=post_page---byline--3349ee5f194c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3349ee5f194c--------------------------------) ·阅读时间5分钟·2024年11月13日

--

![](../Images/dacb530918d21df4c1d2032a4ea04f31.png)

图片由作者提供

我们必须了解我们的模型在其预测中的确定性，才能做出充分知情的决策。因此，仅返回一个点预测是不够的。它无法告诉我们是否可以信任我们的模型。如果你想知道原因，请查看我下面的文章。

[](/uncertainty-quantification-and-why-you-should-care-3f8a651f1956?source=post_page-----3349ee5f194c--------------------------------) [## 不确定性量化及其重要性

### 如何通过三行代码提升你的机器学习模型

towardsdatascience.com](/uncertainty-quantification-and-why-you-should-care-3f8a651f1956?source=post_page-----3349ee5f194c--------------------------------)

在文章中，我以分类问题为例。然而，许多实际问题是回归问题。例如，我们想知道模型在预测明天的温度时的确定性如何。

由于温度是一个连续变量，我们希望知道真实温度将位于哪个区间。

区间越宽，模型的不确定性越大。因此，在做决策时，我们应该减少对其的信任。

# 但我们如何获得这样的预测区间呢？

有两种方法可以考虑。要么使用一组预测区间的模型，要么将点预测转换为……
