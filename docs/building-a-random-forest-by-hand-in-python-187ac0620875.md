# 手动在 Python 中构建随机森林

> 原文：[`towardsdatascience.com/building-a-random-forest-by-hand-in-python-187ac0620875?source=collection_archive---------4-----------------------#2024-01-30`](https://towardsdatascience.com/building-a-random-forest-by-hand-in-python-187ac0620875?source=collection_archive---------4-----------------------#2024-01-30)

## 对一种强大且流行的算法的深入探讨

[](https://mgsosna.medium.com/?source=post_page---byline--187ac0620875--------------------------------)![Matt Sosna](https://mgsosna.medium.com/?source=post_page---byline--187ac0620875--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--187ac0620875--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--187ac0620875--------------------------------) [Matt Sosna](https://mgsosna.medium.com/?source=post_page---byline--187ac0620875--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--187ac0620875--------------------------------) ·17 分钟阅读·2024 年 1 月 30 日

--

![](img/3e3e1bb4d0ea0375209a319ae9478702.png)

图片来源：[FlyD](https://unsplash.com/@flyd2069?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

从 [药物发现](https://www.sciencedirect.com/science/article/abs/pii/S0957417416306819) 到 [物种分类](https://www.mdpi.com/2072-4292/4/9/2661)，从 [信用评分](https://journals.sagepub.com/doi/abs/10.1177/2278533718765531) 到 [网络安全](https://www.sciencedirect.com/science/article/pii/S1877050916311127) 等，随机森林是一个流行且强大的算法，用于建模我们复杂的世界。它的多功能性和预测能力似乎需要最前沿的复杂技术，但如果我们深入了解随机森林的实际构成，我们会发现**它实际上只是一个出乎意料简单的重复步骤集。**

我发现，学习某个东西最好的方式就是动手实践。因此，为了直观理解随机森林的工作原理，我们将手动在 Python 中构建一个，从决策树开始，逐步扩展到完整的森林。我们将亲自体验这个算法在分类和回归中的灵活性和可解释性。虽然这个项目听起来可能很复杂，但我们实际上只需要学习几个核心概念：1）如何迭代地划分数据，以及 2）如何量化数据划分的效果。

# 背景

## 决策树推理

决策树是一种监督学习算法，它识别**一组将特征映射到标签的二元规则分支**。与像逻辑回归这样的算法不同，后者的输出是一个方程，决策树算法是[非参数的](https://machinelearningmastery.com/parametric-and-nonparametric-machine-learning-algorithms/)…
