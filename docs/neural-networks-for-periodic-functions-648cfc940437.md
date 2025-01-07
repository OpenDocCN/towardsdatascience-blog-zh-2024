# 周期性函数的神经网络

> 原文：[https://towardsdatascience.com/neural-networks-for-periodic-functions-648cfc940437?source=collection_archive---------6-----------------------#2024-01-17](https://towardsdatascience.com/neural-networks-for-periodic-functions-648cfc940437?source=collection_archive---------6-----------------------#2024-01-17)

## 当ReLU的外推能力不足时

[](https://dr-robert-kuebler.medium.com/?source=post_page---byline--648cfc940437--------------------------------)[![Dr. Robert Kübler](../Images/3b8d8b88f76c0c43d9c305e3885e7ab9.png)](https://dr-robert-kuebler.medium.com/?source=post_page---byline--648cfc940437--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--648cfc940437--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--648cfc940437--------------------------------) [Dr. Robert Kübler](https://dr-robert-kuebler.medium.com/?source=post_page---byline--648cfc940437--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--648cfc940437--------------------------------) ·阅读时长8分钟·2024年1月17日

--

![](../Images/89e708449fc831a9d049fc0626fb70b9.png)

由[Willian Justen de Vasconcellos](https://unsplash.com/@willianjusten?utm_source=medium&utm_medium=referral)拍摄，发布于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

神经网络被认为是任何函数的优秀逼近器——至少在我们**不偏离数据集太远**时。让我们看看这是什么意思。这里是一些数据：

![](../Images/e5a4fdd5b9374171dc20837f5f11b53e.png)

图片由作者提供。

它不仅仅看起来像正弦波，它实际上就是正弦波，只是加了一些噪声。我们现在可以训练一个普通的前馈神经网络，拥有1个隐藏层，包含1000个神经元并使用ReLU激活。我们得到以下拟合结果：

![](../Images/4ef85ea759ab6e5791b818e1de3b401d.png)

图片由作者提供。

除了边缘部分，效果看起来相当不错。我们*可以*通过根据Cybenko的**普适逼近定理**，向隐藏层添加更多神经元来修复这个问题。但我想给你指出一些其他的东西：

![](../Images/3e1658fbdc701d63a0ea6fd8c7bd13f0.png)

图片由作者提供。

我们*可以*现在辩称，如果假设波形模式在外部继续，这种外推行为是**不好的**……
