# 图像-文本多模态基础模型如何工作

> 原文：[`towardsdatascience.com/how-does-an-image-text-foundation-model-work-05bc7598e3f2?source=collection_archive---------1-----------------------#2024-06-01`](https://towardsdatascience.com/how-does-an-image-text-foundation-model-work-05bc7598e3f2?source=collection_archive---------1-----------------------#2024-06-01)

## 了解图像-文本多模态模型如何执行图像分类、图像检索和图像字幕生成

[](https://jasonweiyi.medium.com/?source=post_page---byline--05bc7598e3f2--------------------------------)![Wei Yi](https://jasonweiyi.medium.com/?source=post_page---byline--05bc7598e3f2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--05bc7598e3f2--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--05bc7598e3f2--------------------------------) [Wei Yi](https://jasonweiyi.medium.com/?source=post_page---byline--05bc7598e3f2--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--05bc7598e3f2--------------------------------) ·阅读时间：23 分钟·2024 年 6 月 1 日

--

![](img/a3306e44f000e5c664851e5fafa2bcf6.png)

图片来源：[Bozhin Karaivanov](https://unsplash.com/@bkaraivanov?utm_source=medium&utm_medium=referral) 于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

现在，基于多模态的基础模型正在快速增长。它们能够理解不同类型的数据，包括文本、图像、视频、音频，并且可以执行需要多种数据模态知识的任务。你是否曾经想过这些模型是如何工作的？

理解多模态模型的关键是弄清楚它如何建立不同数据模态之间的**对齐**。

本文使用了一个简单的图像-文本双模态模型，名为[CoCa](https://arxiv.org/pdf/2205.01917)，来解释这些模型的内部工作原理。我喜欢 CoCa，因为它的设计直观，借鉴了其他一些多模态模型的思想。

# 为什么要使用图像-文本模型？

为什么我们需要一个图像-文本双模态模型？在回答这个问题之前，让我们先来思考一下单模态模型能做什么，不能做什么。

**仅图像模型**

单一图像模态模型，例如[ResNet](https://arxiv.org/abs/1512.03385)，只学习图像信息，并可以执行基本的图像理解任务，比如**图像分类**——将图像分类到某一类别中……
