# 神经网络中的激活函数：如何选择合适的激活函数

> 原文：[https://towardsdatascience.com/activation-functions-in-neural-networks-how-to-choose-the-right-one-cb20414c04e5?source=collection_archive---------10-----------------------#2024-12-12](https://towardsdatascience.com/activation-functions-in-neural-networks-how-to-choose-the-right-one-cb20414c04e5?source=collection_archive---------10-----------------------#2024-12-12)

## 激活函数简介及最著名函数的概览

[](https://medium.com/@niklas_lang?source=post_page---byline--cb20414c04e5--------------------------------)[![Niklas Lang](../Images/5fa71386db00d248438c588c5ae79c67.png)](https://medium.com/@niklas_lang?source=post_page---byline--cb20414c04e5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cb20414c04e5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cb20414c04e5--------------------------------) [Niklas Lang](https://medium.com/@niklas_lang?source=post_page---byline--cb20414c04e5--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cb20414c04e5--------------------------------) ·15分钟阅读·2024年12月12日

--

![](../Images/86c1d96fd9990ce347569964a4b92cf0.png)

图片由[engin akyurt](https://unsplash.com/@enginakyurt?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

近年来，神经网络已成为机器学习模型中一种强大的方法。激活函数是每个神经网络中的核心组件，它显著影响着模型的功能性。它决定了网络中一个神经元的激活强度，从而决定了从数据中学习到哪些结构。如果没有激活函数，神经网络只能识别线性关系，无法产生那些在近年来令我们惊叹的出色结果。

在本文中，我们将详细探讨激活函数的属性，并比较常用的不同激活函数。我们还将提供一些关于如何为网络架构和具体使用案例找到合适的激活函数的建议，以便训练出最优模型。然而，在深入这个话题之前，我们应该先了解神经网络，特别是其中的神经元是如何工作的，以便了解激活函数在何种系统中嵌入。

# 感知机是如何工作的？
