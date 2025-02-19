# 用神经 ODE 建模动态系统：实用指南

> 原文：[`towardsdatascience.com/modeling-dynamical-systems-with-neural-ode-a-hands-on-guide-71c4cfdb84dc?source=collection_archive---------2-----------------------#2024-01-12`](https://towardsdatascience.com/modeling-dynamical-systems-with-neural-ode-a-hands-on-guide-71c4cfdb84dc?source=collection_archive---------2-----------------------#2024-01-12)

## 概念，案例研究，逐步实施

[](https://shuaiguo.medium.com/?source=post_page---byline--71c4cfdb84dc--------------------------------)![Shuai Guo](https://shuaiguo.medium.com/?source=post_page---byline--71c4cfdb84dc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--71c4cfdb84dc--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--71c4cfdb84dc--------------------------------) [Shuai Guo](https://shuaiguo.medium.com/?source=post_page---byline--71c4cfdb84dc--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--71c4cfdb84dc--------------------------------) ·22 分钟阅读·2024 年 1 月 12 日

--

![](img/27e2d2e8db3c82aee9bf1a7e679861ec.png)

由 DALL-E 生成的图片。

对于许多物理系统，正确建模它们的动态行为为系统的理解、预测和控制奠定了基础。从数学上讲，描述系统状态随时间变化速率的**常微分方程**（ODEs）被广泛用于表征动态系统。相应地，许多分析和数值求解器已经被开发出来，可以有效地解决 ODEs，从而使我们能够描绘系统状态轨迹。

然而，使用 ODE 求解器有一个条件：我们需要完全了解 ODEs。对于许多真实世界的复杂系统，不幸的是，这个要求很少被满足：对于一些系统，我们可能知道 ODEs 的函数形式，但不知道相关的模型**参数**；对于其他系统，甚至它们的**函数形式**对我们来说是隐藏的。没有明确的方程，使用 ODE 求解器进行动态系统建模是不可行的。

![](img/ba9e9e7afbbb9003869da7e5d45898a4.png)

图 1。在实践中，许多真实动态系统的主导 ODEs 只有部分已知。（作者提供的图片）

那么如何解决这个问题？
