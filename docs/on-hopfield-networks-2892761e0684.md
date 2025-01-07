# 关于 Hopfield 网络

> 原文：[https://towardsdatascience.com/on-hopfield-networks-2892761e0684?source=collection_archive---------4-----------------------#2024-10-12](https://towardsdatascience.com/on-hopfield-networks-2892761e0684?source=collection_archive---------4-----------------------#2024-10-12)

## 从一般模型到特例

[](https://jagota-arun.medium.com/?source=post_page---byline--2892761e0684--------------------------------)[![Arun Jagota](../Images/3c3eb142f671b5fb933c2826d8ed78d9.png)](https://jagota-arun.medium.com/?source=post_page---byline--2892761e0684--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2892761e0684--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2892761e0684--------------------------------) [Arun Jagota](https://jagota-arun.medium.com/?source=post_page---byline--2892761e0684--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2892761e0684--------------------------------) ·13分钟阅读·2024年10月12日

--

![](../Images/1327ac378509aec1b31238ec96045b95.png)

图片来自[Shubham Dhage](https://unsplash.com/@theshubhamdhage?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)在[Unsplash](https://unsplash.com/photos/a-group-of-cubes-that-are-connected-to-each-other-R2HtYWs5-QA?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

2024年诺贝尔物理学奖授予了John Hopfield 和 Geoffrey Hinton，其中John Hopfield 因其在所谓的 Hopfield 网络方面的工作而获奖。

几十年前，我的博士论文研究的是 Hopfield 风格的网络。我觉得这是写这篇文章的好时机。

我将重点讨论 Hopfield 网络。我会从最简单的例子开始，演示它是如何工作的，然后引入能量函数的概念并在局部最小化它，接着讨论我博士论文中提出的特例，再回到更一般的情况，讨论联想记忆和优化应用，最后再次回到特例，在更具体的场景中实例化这些应用。

让我们从头开始。首先，什么是 Hopfield 网络？

请看下面的图片。

如下解释。我们有两个神经元，每个神经元的取值为1（可以理解为“激活”）或-1（可以理解为“未激活”）。这两个神经元通过一个正权重连接，表示具有正向影响的突触。这意味着，从任意一个神经元的角度来看，假设是第一个神经元，它希望另一个神经元与它保持相同的状态——“激活”或“未激活”。
