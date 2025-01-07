# 对 softmax 函数的视觉理解

> 原文：[`towardsdatascience.com/a-visual-understanding-of-the-softmax-function-b4d92fdaccfa?source=collection_archive---------2-----------------------#2024-11-03`](https://towardsdatascience.com/a-visual-understanding-of-the-softmax-function-b4d92fdaccfa?source=collection_archive---------2-----------------------#2024-11-03)

## softmax 函数的数学原理与直觉，以及它在神经网络和 softmax 回归中的应用

[](https://reza-bagheri79.medium.com/?source=post_page---byline--b4d92fdaccfa--------------------------------)![Reza Bagheri](https://reza-bagheri79.medium.com/?source=post_page---byline--b4d92fdaccfa--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b4d92fdaccfa--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b4d92fdaccfa--------------------------------) [Reza Bagheri](https://reza-bagheri79.medium.com/?source=post_page---byline--b4d92fdaccfa--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b4d92fdaccfa--------------------------------) ·阅读时间 25 分钟·2024 年 11 月 3 日

--

![](img/7dc6407208970404abdcca81e6affe28.png)

图像由 DALL.E 生成

softmax 函数是统计学和机器学习中最重要的函数之一。它接收一个 *K* 维实数向量，并将其转换为一个 *K* 维概率向量，这些概率的总和为 1。Softmax 是 logistic 函数的一个推广，适用于多于两个维度，并且可以用于 softmax 回归（也称为多项式 logistic 回归）来解决具有多个标签的分类问题。softmax 函数还可以作为神经网络在多分类问题中的最后激活函数。在这种情况下，神经网络使用 softmax 激活函数来计算目标每个可能类别的概率。

本文提供了对 softmax 函数的视觉理解，包括其背后的直觉，以及使其在机器学习中具有价值的重要数学性质。我们还讨论了 softmax 函数与 logistic 函数之间的关系，并演示了如何使用 Python 执行 softmax 回归。

*本文中的所有图像均由作者创建。*

## **从 logistic 回归到**…
