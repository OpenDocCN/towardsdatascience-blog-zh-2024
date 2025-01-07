# 使用符号回归在数据中发现隐藏的规律

> 原文：[`towardsdatascience.com/find-hidden-laws-within-your-data-with-symbolic-regression-ebe55c1a4922?source=collection_archive---------5-----------------------#2024-02-09`](https://towardsdatascience.com/find-hidden-laws-within-your-data-with-symbolic-regression-ebe55c1a4922?source=collection_archive---------5-----------------------#2024-02-09)

## 自动发现基本公式，如开普勒定律和牛顿定律

[](https://dr-robert-kuebler.medium.com/?source=post_page---byline--ebe55c1a4922--------------------------------)![Dr. Robert Kübler](https://dr-robert-kuebler.medium.com/?source=post_page---byline--ebe55c1a4922--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ebe55c1a4922--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ebe55c1a4922--------------------------------) [Dr. Robert Kübler](https://dr-robert-kuebler.medium.com/?source=post_page---byline--ebe55c1a4922--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ebe55c1a4922--------------------------------) ·8 分钟阅读·2024 年 2 月 9 日

--

![](img/74474fd9b570bc35d8ac0da6ae3251ea.png)

照片由 [NASA](https://unsplash.com/@nasa?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

作为机器学习的从业者，我们通常会有一个数据集（*X*，*y*），我们想要找到一个函数 *M* — 也称为模型 — 使得 *M*(*X*) ≈ *y*。通常，我们不关心 *M* 的**函数形式**。就我们而言，模型可以是神经网络、基于树的算法，或者是完全不同的东西 —— 只要在测试集上的表现好，我们就很高兴。

然而，如果我们使用像这些复杂的模型，我们可能会错过数据中的有趣模式，甚至可能错失**基本物理规律或经济规律**。为了做得更好，我将向你展示如何使用**符号回归**构建模型。这些模型的特点是，它们只包含少数几项，并且可以在任何地方轻松地（重新）实现。让我们看看我指的是什么。

# 一次物理实验

假设我们是实验物理学家，想要找出一个物体从某个高度 *h* 自由下落到地面所需的时间。例如，如果你从 *h* = 1.5 米的高度丢下一个物体（*足够重，不会受到空气阻力的影响*），它大约需要 *t* = 0.55 秒才能到达地面。试试看吧！
