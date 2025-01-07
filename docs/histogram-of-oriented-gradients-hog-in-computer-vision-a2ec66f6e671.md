# 计算机视觉中的方向梯度直方图（HOG）

> 原文：[`towardsdatascience.com/histogram-of-oriented-gradients-hog-in-computer-vision-a2ec66f6e671?source=collection_archive---------5-----------------------#2024-11-04`](https://towardsdatascience.com/histogram-of-oriented-gradients-hog-in-computer-vision-a2ec66f6e671?source=collection_archive---------5-----------------------#2024-11-04)

## 一种用于目标检测和识别任务的方向梯度直方图（HOG）（特征提取算法）的解释和实现

[](https://medium.com/@hamzzamuhamad?source=post_page---byline--a2ec66f6e671--------------------------------)![Mohamad Hamza](https://medium.com/@hamzzamuhamad?source=post_page---byline--a2ec66f6e671--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a2ec66f6e671--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a2ec66f6e671--------------------------------) [Mohamad Hamza](https://medium.com/@hamzzamuhamad?source=post_page---byline--a2ec66f6e671--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a2ec66f6e671--------------------------------)·阅读 6 分钟·2024 年 11 月 4 日

--

# 介绍

> 方向梯度直方图最初由 Navneet Dalal 和 Bill Trigs 在他们的 CVPR 论文[“[用于人体检测的方向梯度直方图](https://hal.inria.fr/inria-00548512/document)”]中首次引入

有许多不同的特征提取算法，取决于它专注的特征类型，比如纹理、颜色或形状，它是描述整个图像还是仅局部信息。

HOG 算法是特征提取中最基本的技术之一，因为它是目标检测和识别任务的基本步骤。

在本文中，我们将探讨 HOG 算法的原理和实现。

**什么是方向梯度直方图（HOG）？**

HOG 是一种全局描述符（特征提取）方法，应用于图像中的每个像素，以提取像纹理这样的邻域信息（像素的邻域），并将给定图像中的信息压缩/抽象成一个称为特征向量的缩减/压缩形式，该向量可以描述这幅图像的特征，当涉及捕捉图像中的边缘和梯度结构时非常有用。此外，我们可以比较这个…
