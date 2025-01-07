# 准确性与可解释性的权衡是一个谎言

> 原文：[https://towardsdatascience.com/the-accuracy-vs-interpretability-trade-off-is-a-lie-0bf89c84f5b3?source=collection_archive---------5-----------------------#2024-10-16](https://towardsdatascience.com/the-accuracy-vs-interpretability-trade-off-is-a-lie-0bf89c84f5b3?source=collection_archive---------5-----------------------#2024-10-16)

## 为什么，从大局来看，黑盒模型并不更准确

[](https://conorosullyds.medium.com/?source=post_page---byline--0bf89c84f5b3--------------------------------)[![Conor O'Sullivan](../Images/2dc50a24edb12e843651d01ed48a3c3f.png)](https://conorosullyds.medium.com/?source=post_page---byline--0bf89c84f5b3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0bf89c84f5b3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0bf89c84f5b3--------------------------------) [Conor O'Sullivan](https://conorosullyds.medium.com/?source=post_page---byline--0bf89c84f5b3--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0bf89c84f5b3--------------------------------) ·阅读时间：7分钟·2024年10月16日

--

![](../Images/c825d5b59c5d7e78ee67cff1bb29dfd0.png)

图片由[Nathan Cima](https://unsplash.com/@nathan_cima?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

当我开始做数据科学家时，我本以为会使用最先进的模型。XGBoost、神经网络。这些东西复杂且有趣，肯定能带来改进。然而，我并未料到，模型面临的一个难题是——如何向其他人解释它们。

谁能想到你需要理解你的自动化系统所做的决策？

令我高兴的是，我偶然走进了[与模型无关的方法](/what-are-model-agnostic-methods-387b0e8441ef)这个兔子洞。通过这些方法，我可以同时享有两全其美的效果。我可以训练黑盒模型，然后通过像[SHAP](/introduction-to-shap-with-python-d27edc23c454)、[LIME](/a-deep-dive-on-lime-for-local-interpretations-872bea23952f)、[PDPs](/the-ultimate-guide-to-pdps-and-ice-plots-4182885662aa)、[ALEs](/deep-dive-on-accumulated-local-effect-plots-ales-with-python-0fc9698ed0ee)和[Friedman’s H-stat](/understanding-freidmans-h-statistic-h-stat-for-interactions-43fb5e31a586)等方法来解释它们。我们不再需要为了可解释性而牺牲准确性！

不是那么快。那种思维是错误的。

在我们追求最佳性能的过程中，我们常常忽视了机器学习的真正意义：即在新的未见数据上做出准确的预测。让我们来讨论一下，为什么复杂模型并不总是实现这一目标的最佳方法。即使我们可以通过其他方法来解释它们。

# 什么是准确性与可解释性之间的权衡？
