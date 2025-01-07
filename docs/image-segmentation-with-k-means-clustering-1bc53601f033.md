# 使用 K 均值聚类进行图像分割

> 原文：[`towardsdatascience.com/image-segmentation-with-k-means-clustering-1bc53601f033?source=collection_archive---------6-----------------------#2024-09-05`](https://towardsdatascience.com/image-segmentation-with-k-means-clustering-1bc53601f033?source=collection_archive---------6-----------------------#2024-09-05)

## Python 实现简介

[](https://medium.com/@jdhwilkins?source=post_page---byline--1bc53601f033--------------------------------)![James Wilkins](https://medium.com/@jdhwilkins?source=post_page---byline--1bc53601f033--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1bc53601f033--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1bc53601f033--------------------------------) [James Wilkins](https://medium.com/@jdhwilkins?source=post_page---byline--1bc53601f033--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1bc53601f033--------------------------------) ·阅读时长 11 分钟·2024 年 9 月 5 日

--

你可以在[这里](https://github.com/jdhwilkins/Image-Segmentation-K-Means)查看此项目的笔记本。

![](img/06a3c0074647c18c31c798504f13f78a.png)![](img/ffa682f3b09fa9fed5f3e97c0a26246e.png)

左侧：原始照片，右侧：分割图像（5 种颜色/区域）

从上面的图像来看，我们看到一个图像后期处理的例子，这个滤镜让图像呈现卡通风格的外观，但在背后，这个滤镜实际上是使用了一个被称为***聚类***的机器学习算法。

在深入探讨这一过程如何工作以及如何在 Python 中实现之前，让我们先看看为什么我们一开始会想要进行图像分割。

## 图像分割

在普通的照片中，一个像素可以呈现大约 1670 万种不同的颜色。然而，在这张处理过的图像中，只有 5 种不同的颜色。我们将所有像素分成了 5 个不同的组，将图像分割成这些不同的颜色区域。

我们还减少了图像中的噪声和变化量。因此，如果将其用于其他机器学习应用中，我们已经大大减少了需要处理的数据量，特别是如果将其应用于大量图像库时。

尽管我们已经简化了这张图像，但我们仍然保留了大部分重要的结构性数据。我们依然能够识别形状、形式、阴影等信息。
