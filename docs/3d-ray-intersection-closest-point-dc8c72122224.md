# 相交两条3D射线（最近点）

> 原文：[https://towardsdatascience.com/3d-ray-intersection-closest-point-dc8c72122224?source=collection_archive---------8-----------------------#2024-02-12](https://towardsdatascience.com/3d-ray-intersection-closest-point-dc8c72122224?source=collection_archive---------8-----------------------#2024-02-12)

## *让我们解开3D射线背后的数学，展示它其实并不比传统的2D情况更具挑战性。*

[](https://medium.com/@thom01.rouch?source=post_page---byline--dc8c72122224--------------------------------)[![Thomas Rouch](../Images/a8440bbed59cd8d9cdd752cf1fea2831.png)](https://medium.com/@thom01.rouch?source=post_page---byline--dc8c72122224--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--dc8c72122224--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--dc8c72122224--------------------------------) [Thomas Rouch](https://medium.com/@thom01.rouch?source=post_page---byline--dc8c72122224--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dc8c72122224--------------------------------) ·7分钟阅读·2024年2月12日

--

![](../Images/2e951c331eed729eaafe73a0d22c9723.png)

摄影：由 [Tobias Cornille](https://unsplash.com/@tobiasc?utm_source=medium&utm_medium=referral) 拍摄，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 1\. 介绍

## 动机

在计算机图形学、自动驾驶导航或增强现实等领域，通过操控3D环境来创造沉浸式体验，深刻理解射线相交和三角剖分算法，使数据科学家能够制作栩栩如生的模拟和互动虚拟世界。

它还展示了诸如伪逆矩阵、最小二乘法优化、克莱姆法则或三重标量积等数学原理，不仅仅是理论；它们是解决现实世界问题的实际工具！

## 射线定义

射线从给定位置开始，并沿着给定方向无限延伸，就像激光束或光线一样。

> 可以把射线看作是单行道，有一个固定的起点，而直线则像一条双向街道，无限延伸。

从数学上讲，一条射线由其起点`o`和方向`d`定义。由于射线是1维空间，射线上任意一点可以通过其相对于原点的正距离（或时间）`t`来参数化。

![](../Images/b203aab18b69d9958f77df43a81e9975.png)

## 射线相交

找到两条射线`r1`和`r2`的交点意味着找到两个参数`t1`和`t2`，分别位于第一条和第二条射线上，使它们在空间中结果为完全相同的点。

![](../Images/fa6b317505532185f1eb8b32ccdf6611.png)

一个有趣的点是，我们并没有指定光线所在空间的维度。它可以是二维的，比如纸上的一条直线，三维的，如激光束，甚至可以存在于更高维度的空间中！

由于我们要找的是`t1`和`t2`的值，我们将它们连接成一个列向量`x`。这使我们能够将这个方程向量化成标准的线性系统`Ax=b`。为了更清晰，每个项下方都有注释，指明了矩阵或向量的形状，其中`n`是定义光线的空间维度。

![](../Images/3c2ddf4a94f00d44736eae162b06d844.png)

> 注意：矩阵A是通过将两个方向作为列向量并排连接构建的，第二个方向是反向的。

解线性方程`Ax=b`可以得到`t1`和`t2`。然而，如果这两个参数中的任何一个是负数，则表示交点位于某个光线的原点之后，意味着两条光线之间没有交点。

![](../Images/8fb70caa8ddffaa0cae50ec66ecc0fc9.png)

图片由[Mehdi MeSSrro](https://unsplash.com/@messrro?utm_source=medium&utm_medium=referral)拍摄，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 2\. 2D光线相交

## 引言

2D情况是最简单的。通常，除非它们完全平行，否则两条二维线总是会在一个唯一的公共点相交。

在下图中，`r1`和`r2`相交。然而，`r3`没有与任何光线相交，因为它与`r2`平行，并且在原点之后与`r1`相交。

![](../Images/df05979d9169ef6e2dcab4efa40e235f.png)

2D光线相交——图示由作者提供

## 解线性系统

上面介绍的线性方程`Ax=b`有`n`个方程和2个未知数。因此，在二维情况下，我们最终得到的是一个有2个方程和2个未知数的系统，这意味着我们只需要求解矩阵`A`的逆。

![](../Images/7524335da956aea1d4916fa9e6155511.png)

如果光线是平行的，矩阵`A`将不可逆，因为它的两列，`d1`和`-d2`，是共线的。

如果你不关心`t1`和`t2`，只想知道是否存在相交，计算矩阵`A`的行列式并将其与0比较就是你所需要的全部。

如果你不关心同时得到`t1`和`t2`，例如如果你只关心交点的实际二维位置，使用克拉梅法则是一个聪明的技巧。它通过矩阵`A`和修改过的矩阵的行列式来表示解，其中`A`的其中一列被`b`替换。

![](../Images/f0408dbffe70f7d8077ce6d0bd328742.png)

克拉梅法则用于识别`t1`和`t2`。运算符`|.|`表示行列式。逗号用于分隔矩阵列。

![](../Images/7915c7a151b0da57ffde63bd3b2a1c73.png)

图片由[Vince Fleming](https://unsplash.com/@vincefleming?utm_source=medium&utm_medium=referral)拍摄，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 3D光线相交

## 引言

与二维情况不同，两个三维射线相交的可能性非常小。例如，试图让两个人手持的激光指示器相交是非常具有挑战性的。

线性系统`Ax=b`最终有3个方程和2个未知数，除非方程中有冗余，否则无法求解。

## 最小二乘法

由于大多数情况下没有解，两个射线之间的最短线段可以视为它们的交点。我们将`p1`和`p2`分别表示为位于`r1`和`r2`上的这段最优线段的端点。

![](../Images/4894170beb6c75c51efbae2d6f75a13a.png)

3D射线交点 — 作者图示

我们现在的任务转化为一个最小二乘法优化问题。由于`Ax-b`已经表示了`p2`和`p1`之间的向量，所以其公式化非常简单。

![](../Images/6d39d07af994d5b66dbde274fe1eb9fe.png)

这是一个众所周知的问题。在最优解处将梯度归零可以通过伪逆矩阵得到解，这是构建一个方阵并使其可逆的便捷方法。

![](../Images/1ced4f49ef7b4ff584077985aa5387e8.png)![](../Images/a6ec95ccf0a2d28401791aef1556ebac.png)

## 正交性

尽管在上面的图示中最短线段似乎垂直于两条射线，但我们还未正式证明这一点，尽管这看起来是直观的。

零梯度也意味着由`p1`和`p2`定义的线段与两个射线方向`d1`和`d2`之间的点积为零，从而证明了正交性。

![](../Images/e2faa09fa96ff1eec553d68ebe4d10a1.png)

## 替代线性系统

最小二乘法方法完全有效，但我们也可以引入第三个未知变量，使得我们的线性系统可以求解。

考虑到向量`d1`、`d2`和`p2-p1`构成了一个正交基，我们可以以`o1`为原点构建一个正交框架。然后，可以通过表达`o2`相对于该框架的局部坐标来推导出点`p1`和`p2`。

请注意，线段`p2-p1`的方向由两个射线方向的叉积给出。我们将在该方向上引入带符号的比例δ，并且在正交基中使用该比例。两条射线之间的实际距离可以通过将|δ|与叉积的范数相乘得到。

![](../Images/aa98e49dbef95149487fb995fd4046b9.png)

t1、δ和t2可以通过线性方程组求解。

![](../Images/4d7b7ddccdaff497a2b02f78cd18d4aa.png)

3x3矩阵的行列式可以通过标量三重积来推导，标量三重积涉及一个列向量与其他两个列向量的叉积的点积。

![](../Images/b20e72c430c427bae012dfff6fa8e9bc.png)

在我们的例子中，简化行列式方程是直接的，因为第二列已经是叉积。请注意，将叉积插入为最后一列将导致范数的否定。

![](../Images/8fe3951f383fdbfd31267ff4acd218a9.png)

使用克拉默法则求解可以得到最优的`t1`和`t2`，还可以得到带符号的尺度δ。

![](../Images/01a30c132c755c622503cb50496ebbe2.png)

如果你喜欢简洁的方程式，可以重新排列和交换列，去除负号，使其看起来更整洁！

![](../Images/168bcd7adb3da59d325b4d21709a8d84.png)

## 交点

交点可以定义为最短线段的中点，即`0.5(p1+p2)`。

## 射线之间的距离

如前所述，带符号的尺度δ必须乘以两个射线叉积的模长，才能获得射线之间的真实距离。

![](../Images/89088b762943928dfe2be9d4f8d8b6c1.png)

# 结论

希望你喜欢阅读这篇文章，并且它能为你提供更多关于如何求交2D或3D射线的洞见！
