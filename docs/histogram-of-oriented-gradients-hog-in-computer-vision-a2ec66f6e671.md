# 计算机视觉中的方向梯度直方图（HOG）

> 原文：[https://towardsdatascience.com/histogram-of-oriented-gradients-hog-in-computer-vision-a2ec66f6e671?source=collection_archive---------5-----------------------#2024-11-04](https://towardsdatascience.com/histogram-of-oriented-gradients-hog-in-computer-vision-a2ec66f6e671?source=collection_archive---------5-----------------------#2024-11-04)

## 一种用于目标检测和识别任务的方向梯度直方图（HOG）（特征提取算法）的解释和实现

[](https://medium.com/@hamzzamuhamad?source=post_page---byline--a2ec66f6e671--------------------------------)[![Mohamad Hamza](../Images/e080580382bafedf109d8f95975d42c5.png)](https://medium.com/@hamzzamuhamad?source=post_page---byline--a2ec66f6e671--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a2ec66f6e671--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a2ec66f6e671--------------------------------) [Mohamad Hamza](https://medium.com/@hamzzamuhamad?source=post_page---byline--a2ec66f6e671--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a2ec66f6e671--------------------------------)·阅读6分钟·2024年11月4日

--

# 介绍

> 方向梯度直方图最初由Navneet Dalal和Bill Trigs在他们的CVPR论文[“[用于人体检测的方向梯度直方图](https://hal.inria.fr/inria-00548512/document)”]中首次引入

有许多不同的特征提取算法，取决于它专注的特征类型，比如纹理、颜色或形状，它是描述整个图像还是仅局部信息。

HOG算法是特征提取中最基本的技术之一，因为它是目标检测和识别任务的基本步骤。

在本文中，我们将探讨HOG算法的原理和实现。

**什么是方向梯度直方图（HOG）？**

HOG是一种全局描述符（特征提取）方法，应用于图像中的每个像素，以提取像纹理这样的邻域信息（像素的邻域），并将给定图像中的信息压缩/抽象成一个称为特征向量的缩减/压缩形式，该向量可以描述这幅图像的特征，当涉及捕捉图像中的边缘和梯度结构时非常有用。此外，我们可以比较这个…
