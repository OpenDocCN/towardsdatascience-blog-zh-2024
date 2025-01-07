# 一个周末AI项目：在PC和树莓派上使用YOLO进行物体检测

> 原文：[https://towardsdatascience.com/a-weekend-ai-project-object-detection-with-yolo-on-pc-and-raspberry-pi-0653e01032c1?source=collection_archive---------1-----------------------#2024-07-06](https://towardsdatascience.com/a-weekend-ai-project-object-detection-with-yolo-on-pc-and-raspberry-pi-0653e01032c1?source=collection_archive---------1-----------------------#2024-07-06)

## 在不同硬件上运行最新的YOLO v10模型

[](https://dmitryelj.medium.com/?source=post_page---byline--0653e01032c1--------------------------------)[![Dmitrii Eliuseev](../Images/7c48f0c016930ead59ddb785eaf3e0e6.png)](https://dmitryelj.medium.com/?source=post_page---byline--0653e01032c1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0653e01032c1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0653e01032c1--------------------------------) [Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--0653e01032c1--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0653e01032c1--------------------------------) ·8分钟阅读·2024年7月6日

--

![](../Images/18772400e4457ea9b3d504a182f2b140.png)

YOLO物体检测，图像由作者提供

计算机视觉可以成为不同规模机器学习应用的重要组成部分，从价值2万美元的特斯拉机器人或自动驾驶汽车，到智能门铃和吸尘器。这也是一项具有挑战性的任务，因为与云基础设施相比，在“真实”的边缘设备上，硬件规格通常受到更大限制。

YOLO（You Only Look Once）是一个流行的物体检测库；它的第一个版本发布于2015年。YOLO特别适用于嵌入式设备，因为它几乎可以在任何地方运行；不仅有Python版本，还有C++（ONNX和OpenVINO）和Rust版本。去年，我曾在树莓派4上[测试过YOLO v8](/yolo-object-detection-on-the-raspberry-pi-6de3629256fa)。如今，许多事情发生了变化——新的树莓派5发布了，更新的YOLO v10也发布了。所以我期待在新硬件上，新的模型能够更快速、更精确地运行。

本文中展示的代码是跨平台的，因此没有树莓派的读者也可以在Windows、Linux或OS X电脑上运行它。

不再多说，让我们来看看它是如何工作的！

## 树莓派

对于那些可能从未听说过树莓派的人，让我们简要介绍一下…
