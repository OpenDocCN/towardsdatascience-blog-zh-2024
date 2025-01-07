# 因果推断的简单方法

> 原文：[https://towardsdatascience.com/easy-methods-for-causal-inference-bb5d3da4f8ca?source=collection_archive---------2-----------------------#2024-02-20](https://towardsdatascience.com/easy-methods-for-causal-inference-bb5d3da4f8ca?source=collection_archive---------2-----------------------#2024-02-20)

## 将你最喜欢的模型与元学习者结合起来，做出有效的因果推断

[](https://dr-robert-kuebler.medium.com/?source=post_page---byline--bb5d3da4f8ca--------------------------------)[![Dr. Robert Kübler](../Images/3b8d8b88f76c0c43d9c305e3885e7ab9.png)](https://dr-robert-kuebler.medium.com/?source=post_page---byline--bb5d3da4f8ca--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bb5d3da4f8ca--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bb5d3da4f8ca--------------------------------) [Dr. Robert Kübler](https://dr-robert-kuebler.medium.com/?source=post_page---byline--bb5d3da4f8ca--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bb5d3da4f8ca--------------------------------) ·阅读时间10分钟·2024年2月20日

--

![](../Images/b4c61e7acf7579403b0c0d5dcd23d636.png)

图片由[Mika Baumeister](https://unsplash.com/@kommumikation?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

想象一下，你已经建立了一个很棒的机器学习模型，能够准确预测你的目标值。在某些情况下，这时你的工作可能就完成了。然而，通常业务不仅仅想知道**会**发生什么，还想知道如何**影响**结果。正如格言所说：

> 知道未来是银，而能够改变未来则是金。

这个简单的真理不言而喻，你从个人生活中就能体会到。知道下周的彩票号码很好，但只有当你能够**调整**你的号码时，这才有意义。

以商业为例，考虑**客户流失**的问题，即停止与您做生意的客户。知道客户想要离开您是好的，但真正的问题是：如何防止这个客户流失？

业务需要某种形式的**干预**，例如通过发放优惠券或提供某种会员升级。这些是业务可以影响的措施，目的是降低流失的概率。

如果*x* = “给客户发优惠券”，*y* = 流失概率，我们希望能够……
