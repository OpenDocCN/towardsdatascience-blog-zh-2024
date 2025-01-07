# 论文解析：Vision Transformer（ViT）

> 原文：[`towardsdatascience.com/paper-walkthrough-vision-transformer-vit-c5dcf76f1a7a?source=collection_archive---------2-----------------------#2024-08-13`](https://towardsdatascience.com/paper-walkthrough-vision-transformer-vit-c5dcf76f1a7a?source=collection_archive---------2-----------------------#2024-08-13)

## 通过从零开始的 PyTorch 实现探索 Vision Transformer（ViT）。

[](https://medium.com/@muhammad_ardi?source=post_page---byline--c5dcf76f1a7a--------------------------------)![Muhammad Ardi](https://medium.com/@muhammad_ardi?source=post_page---byline--c5dcf76f1a7a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c5dcf76f1a7a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c5dcf76f1a7a--------------------------------) [Muhammad Ardi](https://medium.com/@muhammad_ardi?source=post_page---byline--c5dcf76f1a7a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c5dcf76f1a7a--------------------------------) ·阅读时长 19 分钟 ·2024 年 8 月 13 日

--

![](img/00cee3371ad23d4932c8e123ee815157.png)

图片来源：[v2osk](https://unsplash.com/@v2osk?utm_source=medium&utm_medium=referral) via [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# **简介**

Vision Transformer —— 通常缩写为 ViT —— 可以被视为计算机视觉领域的一个突破。在与视觉相关的任务中，通常使用基于 CNN 的模型，而这些模型迄今为止总是比任何其他类型的神经网络表现得更好。直到 2020 年，一篇名为“*一张图片值 16×16 个单词：用于大规模图像识别的 Transformer*”的论文由 Dosovitskiy 等人 [1] 发布，才提供了比 CNN 更强的能力。

在 CNN 中，单个卷积层通过使用卷积核提取特征。由于卷积核的大小相对于输入图像较小，因此它只能捕捉到该小区域内的信息。换句话说，我们可以简单地说它侧重于提取局部特征。为了理解图像的全局上下文，需要堆叠多个卷积层。ViT 通过直接从初始层捕获全局信息来解决这个问题。因此，在 ViT 中堆叠多个层可以实现更加全面的信息提取。
