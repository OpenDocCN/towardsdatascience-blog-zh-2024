# 相交多个 3D 线段（最近点）

> 原文：[https://towardsdatascience.com/intersect-multiple-3d-lines-closest-point-93e06b296f15?source=collection_archive---------11-----------------------#2024-03-27](https://towardsdatascience.com/intersect-multiple-3d-lines-closest-point-93e06b296f15?source=collection_archive---------11-----------------------#2024-03-27)

## 寻找一批 3D 线段的交点，更像是一个最小化问题，而不是一个实际的交点测试，正如通常与两条光线做的那样。

[](https://medium.com/@thom01.rouch?source=post_page---byline--93e06b296f15--------------------------------)[![Thomas Rouch](../Images/a8440bbed59cd8d9cdd752cf1fea2831.png)](https://medium.com/@thom01.rouch?source=post_page---byline--93e06b296f15--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--93e06b296f15--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--93e06b296f15--------------------------------) [Thomas Rouch](https://medium.com/@thom01.rouch?source=post_page---byline--93e06b296f15--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--93e06b296f15--------------------------------) ·阅读时间：5 分钟·2024年3月27日

--

![](../Images/23b99afae4ea83fbbf90d039e7880ddd.png)

摄影师：[Baudouin Wisselmann](https://unsplash.com/@baud_wisselmann?utm_source=medium&utm_medium=referral) 来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 1\. 引言

## 动机

了解多个 3D 线段相交的位置在 3D 重建或增强现实等领域非常有用。例如，它帮助我们通过多视角的 2D 图像检测来三角化一个 3D 点，或者找到相机围绕物体旋转时的主要 3D 关注点。

尽管每一行可能包含一些噪音，但拥有更多的行有助于减少变异性，就像我们计算多个样本的平均值一样。

## 两条光线 VS 多条光线

在之前的一篇文章中，[***相交 3D 光线（最近点）***](https://medium.com/towards-data-science/3d-ray-intersection-closest-point-dc8c72122224)，我解释了如何使一对 3D 光线相交。但是，如果我们想让 `n` 条光线相交呢？正如我们在本文中所看到的，从两条光线到 `n` 条光线不仅仅是一个推广，它在本质上是不同的。

![](../Images/1b07c39a95a5ac807f401cb8542537f5.png)

两条光线相交 VS 多条光线相交 — 图由作者提供

提出一个问题本身就意味着这个问题值得被问。因此，寻找一组 3D 光线的交点，本质上暗示着这样一个共同的汇聚点的存在，即使有一些噪音。

> 快速提醒：从数学角度看，射线由其原点`*o*`和方向`*d*`定义。由于它是1维空间，射线上的任何点都可以通过与原点的正距离（或时间）`*t*`来进行参数化。

![](../Images/b203aab18b69d9958f77df43a81e9975.png)

在两条射线的情况下，我们求解每条射线在最接近的点处的参数化`t1`和`t2`，然后从中判断射线是否相交，即`t1>0`且`t2>0`。由于最优的`t1`和`t2`可以独立计算，这种方法也允许提前停止，例如如果`t1<0`，则跳过`t2`的计算。

在`n`条射线的情况下，我们可以假设射线相交，并直接求解3D交点，而不是求解每条射线上的`n`个最优参数化。因此，这更像是一个最小化问题，而不是一个实际的交点测试。

## 射线还是直线？

由于我们没有检查每条射线上的参数化，更准确地说，应该讨论直线而非射线；因此，文章的标题为直线。直线就是没有`t`约束的射线。

![](../Images/cf400ffcbad1d9aadc8070d6c7e1efc5.png)

图片由[Ben Wicks](https://unsplash.com/@profwicks?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 2. 解析解

## 有用的向量技巧

在深入讨论主题之前，我想快速讨论一个对我们之后非常有用的向量技巧。

通过点积来缩放向量也可以看作是矩阵-向量乘积。由于乘积运算符的结合性，项确实可以重新排列。

这将帮助我们通过突出变量`c`的线性关系来因式分解方程。

![](../Images/c1d4319308900121a6598c77f4ebe650.png)

## 最小化问题

交点可以定义为3D点`x`，它最小化与每条输入射线的平方距离之和。

![](../Images/d403e6a27a2ff2bcc0d3e96325c0e595.png)

## 点到直线的正交投影

从射线原点`o`到点`x`的向量可以表示为两个正交向量的和。如下面的图所示，第一个向量沿着直线从`o`到`p`，而第二个向量从`p`到`x`。

一旦我们知道这个点`p`的坐标，即`x`的投影，我们就可以通过计算向量`x-p`的范数来推导出点`x`到直线的距离。

![](../Images/64f27de9b05c63410518f2bef96796a2.png)

点-直线距离 — 作者插图

让我们计算`p`。

如下面的方程组所示，点`p`由两个关键属性定义：它属于直线（具有未知射线参数`tp`），并且`x-p`与直线正交。

![](../Images/0fb7bb8ad8a1414552434596ea33ed5c.png)

将射线参数化引入第二个方程中，可以得到`tp`的值，它是`x-o`和`d`的点积。

> 注意：我们假设射线方向`d`已经被归一化。

![](../Images/1c44f02776cff66034693bcfb45907a9.png)

我们现在可以将其代回射线参数化，来恢复点`p`。

![](../Images/06e5b5e3e9e225349ed2706a4bd4dffd.png)

前面提到的向量技巧在这里很有用，可以强调关于`x-o`的线性关系，这将有助于因式分解。

![](../Images/7b04856deed4550341f6d8420785b00a.png)

## 点-线距离

如前所述，点-线距离现在可以通过计算`x-p`的范数来得到。注意到`x-o`中的优美因式分解。

![](../Images/027e5ab503c42eef4076e4784444edd0.png)

## 损失函数

点-线距离公式为我们提供了我们想要最小化的损失函数`L`。

![](../Images/c9728f659328ff17ac5550964f64766e.png)

`x-o`中的因式分解使得我们可以很容易地推导出它相对于`x`的梯度。

![](../Images/a7e5da2ad869b81e396dbaad7c533648.png)

现在可以通过确定梯度为零的点来获得这个凸损失函数的最小值。

![](../Images/e9a31697717faecae4cc637d755618af.png)

最终我们得到一个线性`3x3`方程组`Ax=b`，其中矩阵`A`和向量`b`的定义如下：

![](../Images/184740c7a3c7faa5ffe524f5425ef3dd.png)

因此，最接近所有射线的3D点可以通过求逆`A`来获得。

![](../Images/472bb130b9bc6c9d2050a35ad7d6d48f.png)

# 结论

最小二乘法最小化通常可以简化为一个普通的正态方程，其中伪逆矩阵用于将`n`个观测值聚合成一个方阵可逆矩阵。

值得一提的是，我们在这里并不需要伪逆，因为矩阵的平方形状自然地来源于射线方向外积的和。

希望你喜欢阅读这篇文章，并且它能为你提供更多关于如何求交多条3D射线的见解！
