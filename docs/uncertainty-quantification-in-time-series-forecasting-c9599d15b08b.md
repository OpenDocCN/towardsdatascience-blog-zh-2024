# 时间序列预测中的不确定性量化

> 原文：[https://towardsdatascience.com/uncertainty-quantification-in-time-series-forecasting-c9599d15b08b?source=collection_archive---------2-----------------------#2024-12-09](https://towardsdatascience.com/uncertainty-quantification-in-time-series-forecasting-c9599d15b08b?source=collection_archive---------2-----------------------#2024-12-09)

## 深入探讨EnbPI，这是一种用于时间序列预测的符合预测方法

[](https://medium.com/@jodancker?source=post_page---byline--c9599d15b08b--------------------------------)[![Jonte Dancker](../Images/29e37a1a1cabc15cfb90a860b2931f03.png)](https://medium.com/@jodancker?source=post_page---byline--c9599d15b08b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c9599d15b08b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c9599d15b08b--------------------------------) [Jonte Dancker](https://medium.com/@jodancker?source=post_page---byline--c9599d15b08b--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c9599d15b08b--------------------------------) ·阅读时间 10 分钟 ·2024年12月9日

--

![](../Images/5098798089e55a35502d6f47fc141ba3.png)

作者提供的图片

我最近的许多文章都围绕着如何了解模型对其预测的信心展开。如果我们知道预测的不确定性，我们可以做出更有信息支持的决策。我向你展示了如何使用**符合预测**（Conformal Prediction）来量化模型的不确定性。我写了关于分类和回归问题的符合预测方法的文章。

[](/calibrating-classification-probabilities-the-right-way-da935caee18d?source=post_page-----c9599d15b08b--------------------------------) [## 正确校准分类概率的方法](https://towardsdatascience.com/calibrating-classification-probabilities-the-right-way-da935caee18d?source=post_page-----c9599d15b08b--------------------------------)

### 或者为什么你不应该信任`predict_proba`方法

[增加回归模型信任的简易方法](https://towardsdatascience.com/increase-trust-in-your-regression-model-the-easy-way-3349ee5f194c?source=post_page-----c9599d15b08b--------------------------------)

### 如何使用**符合量化回归**（Conformalized Quantile Regression）

[增加回归模型信任的简易方法](https://towardsdatascience.com/increase-trust-in-your-regression-model-the-easy-way-3349ee5f194c?source=post_page-----c9599d15b08b--------------------------------)

对于这些方法，我们假设观测的顺序不重要，即数据是可交换的。这对于分类和回归问题是合理的。然而，这一假设在时间序列问题中并不成立。在这里，观测的顺序通常包含重要的信息，例如趋势...
