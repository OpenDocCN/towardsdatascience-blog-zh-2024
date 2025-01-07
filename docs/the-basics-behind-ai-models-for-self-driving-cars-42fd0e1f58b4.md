# 自动驾驶汽车背后的AI模型基础

> 原文：[https://towardsdatascience.com/the-basics-behind-ai-models-for-self-driving-cars-42fd0e1f58b4?source=collection_archive---------8-----------------------#2024-09-19](https://towardsdatascience.com/the-basics-behind-ai-models-for-self-driving-cars-42fd0e1f58b4?source=collection_archive---------8-----------------------#2024-09-19)

## 学习如何使用Python中的PyTorch构建一个能够驾驶的神经网络

[](https://ds-claudia.medium.com/?source=post_page---byline--42fd0e1f58b4--------------------------------)[![Claudia Ng](../Images/81d7927943f38f6303690cfd5676c8fd.png)](https://ds-claudia.medium.com/?source=post_page---byline--42fd0e1f58b4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--42fd0e1f58b4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--42fd0e1f58b4--------------------------------) [Claudia Ng](https://ds-claudia.medium.com/?source=post_page---byline--42fd0e1f58b4--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--42fd0e1f58b4--------------------------------) ·8分钟阅读·2024年9月19日

--

![](../Images/e4573108f66890ea73f1a0f9ec1887f1.png)

途中搭乘无人驾驶出租车（图像来源：作者）

我最近在旧金山体验了我的第一次无人驾驶出租车之旅。

我坐进后座，驾驶座是空的。

我目瞪口呆地看着汽车在停牌时发出右转的信号，并等待行人通过。然后，它慢慢加速，方向盘向右转动。

这真是一次非常平稳的旅程。不再需要担心把我的生命交给一个昏昏欲睡或脾气暴躁的出租车司机。

作为一名数据科学家，我对驱动自动驾驶车辆的技术感到着迷。所以，我学会了构建一个简单的神经网络，可以预测如何驾驶，我会带你一起了解这个过程。

# 控制车辆的基础知识

我们首先需要理解软件和硬件组件是如何协同工作的。

一辆车在水平面上行驶，可以朝四个不同的方向行驶。因此，这辆车配备了传感器，用来检测它在四个方向上与物体的距离：

+   与后方物体的距离，

+   与后方物体的距离，

+   与前方物体的距离，
