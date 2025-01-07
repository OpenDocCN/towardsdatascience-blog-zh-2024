# 深入探讨LIME的局部解释

> 原文：[https://towardsdatascience.com/a-deep-dive-on-lime-for-local-interpretations-872bea23952f?source=collection_archive---------7-----------------------#2024-06-26](https://towardsdatascience.com/a-deep-dive-on-lime-for-local-interpretations-872bea23952f?source=collection_archive---------7-----------------------#2024-06-26)

## 本地可解释模型无关解释（LIME）的直觉、理论和代码

[](https://conorosullyds.medium.com/?source=post_page---byline--872bea23952f--------------------------------)[![Conor O'Sullivan](../Images/2dc50a24edb12e843651d01ed48a3c3f.png)](https://conorosullyds.medium.com/?source=post_page---byline--872bea23952f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--872bea23952f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--872bea23952f--------------------------------) [Conor O'Sullivan](https://conorosullyds.medium.com/?source=post_page---byline--872bea23952f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--872bea23952f--------------------------------) ·13分钟阅读·2024年6月26日

--

![](../Images/800d5fcac06bc9ac4a9fdf642edb0864.png)

（来源：DALL.E）

LIME是[XAI](/what-is-interpretable-machine-learning-2d217b62185a)方法的始祖。它使我们能够理解机器学习模型是如何工作的。具体来说，它可以帮助我们理解个别预测是如何做出的（即局部解释）。

尽管近年来的进展意味着LIME的受欢迎程度有所下降，但它仍然值得理解。这是因为它是一种相对简单的方法，并且对于许多可解释性问题来说“足够好”。它也是一种更近期局部可解释性方法——[SHAP](/introduction-to-shap-with-python-d27edc23c454)的灵感来源。

所以我们将：

+   讨论LIME为获得局部解释所采取的步骤。

+   详细讨论与你选择的步骤相关的一些决策，包括如何为样本加权以及使用哪种代理模型。

+   应用[lime Python包](https://github.com/marcotcr/lime)。

在此过程中，我们将把该方法与SHAP进行比较。这是为了更好地理解它的弱点。我们还将看到，尽管LIME是一个局部方法，我们仍然可以聚合LIME的权重来获得全局解释。这样做将帮助我们理解该包所做的一些默认选择。
