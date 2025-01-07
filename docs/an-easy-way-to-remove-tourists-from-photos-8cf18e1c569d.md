# 从照片中轻松去除游客的方法

> 原文：[`towardsdatascience.com/an-easy-way-to-remove-tourists-from-photos-8cf18e1c569d?source=collection_archive---------6-----------------------#2024-09-02`](https://towardsdatascience.com/an-easy-way-to-remove-tourists-from-photos-8cf18e1c569d?source=collection_archive---------6-----------------------#2024-09-02)

## 快速成功的数据新闻

## 使用 Python、PIL 和 OpenCV 进行图像清理

[](https://medium.com/@lee_vaughan?source=post_page---byline--8cf18e1c569d--------------------------------)![Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--8cf18e1c569d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8cf18e1c569d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8cf18e1c569d--------------------------------) [Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--8cf18e1c569d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8cf18e1c569d--------------------------------) ·阅读时间：8 分钟·2024 年 9 月 2 日

--

![](img/c98c6a5be5bb1f9065e39a7aa6e13380.png)

DALL-E3 拍摄的泰姬陵照片

如果你拍摄著名地标的照片，或者进行天文摄影，你可能听说过*图像叠加*。这是一种将多张静态照片叠加（平均）在一起的过程，以去除噪声或其他不需要的元素。

这些技术可以去除照片站点上任何移动的物体，包括人。比如，Adobe Photoshop 就有一个“人群去除”脚本，能够神奇地消失不稳定的物体。它依赖于一种称为*中位数*的统计平均值，简单来说，就是按从小到大的顺序排列数字后处于“中间”位置的值。

这个过程需要多张照片。最好使用三脚架拍摄，这样你想去除的物体在不同的图像中会发生位置变化，而背景保持不变。通常需要 10 到 30 张相隔大约 20 秒的照片，或者从视频中提取的间隔相似的帧。

使用*均值*时，你*将数字相加*并除以总数。而使用*中位数*时，你*将数字排序*并选择中间的值。

在下图中，展示了一排五张图像，所有图像中的同一像素位置被标出…
