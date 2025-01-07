# 自定义物体检测：探索 YOLO 基本原理并在自定义数据上训练

> 原文：[https://towardsdatascience.com/object-detection-55499ef46a12?source=collection_archive---------8-----------------------#2024-01-08](https://towardsdatascience.com/object-detection-55499ef46a12?source=collection_archive---------8-----------------------#2024-01-08)

## 利用预训练模型、增强图像与边界框，揭示卷积神经网络在物体检测中的强大力量

[](https://guenterroehrich.medium.com/?source=post_page---byline--55499ef46a12--------------------------------)[![Günter Röhrich](../Images/31a1d0dc835c7ad31197f8c387023d10.png)](https://guenterroehrich.medium.com/?source=post_page---byline--55499ef46a12--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--55499ef46a12--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--55499ef46a12--------------------------------) [Günter Röhrich](https://guenterroehrich.medium.com/?source=post_page---byline--55499ef46a12--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--55499ef46a12--------------------------------) ·15 分钟阅读·2024年1月8日

--

深度学习在过去十年中取得了巨大的进展，尽管早期的模型难以理解和应用，但现代框架和工具使得每个拥有一定代码理解的人都可以为计算机视觉任务训练自己的神经网络。

在本文中，我将全面展示如何加载和增强数据以及边界框，训练物体检测算法，并最终查看我们在测试图像中检测物体的准确性。尽管现有的工具包随着时间的推移变得更易于使用，但仍然存在一些可能遇到的陷阱。

# 计算机视觉（CV）简介

计算机视觉是一个非常流行且更为广泛的研究与应用领域。深度学习的进展，尤其是过去十年，极大地加速了我们对深度学习的理解以及它广泛的应用潜力。

为什么我们现在看到这些进展？正如 Keras 库的创始人 François Chollet 所描述的那样，我们目睹了 CPU 计算能力的提升，增长了大约 5000 倍，正是由于…
