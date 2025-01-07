# Vision Mamba：像Vision Transformer，但更强大

> 原文：[https://towardsdatascience.com/vision-mamba-like-a-vision-transformer-but-better-3b2660c35848?source=collection_archive---------5-----------------------#2024-09-16](https://towardsdatascience.com/vision-mamba-like-a-vision-transformer-but-better-3b2660c35848?source=collection_archive---------5-----------------------#2024-09-16)

## [🐍 走向马姆巴状态空间模型：图像、视频与时间序列](https://towardsdatascience.com/tagged/mamba-image-video-signal)

## 第4部分 — 走向马姆巴状态空间模型：图像、视频与时间序列

[](https://medium.com/@SaschaKirch?source=post_page---byline--3b2660c35848--------------------------------)[![Sascha Kirch](../Images/a0d45da9dc9c602075b2810786c660c9.png)](https://medium.com/@SaschaKirch?source=post_page---byline--3b2660c35848--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3b2660c35848--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3b2660c35848--------------------------------) [Sascha Kirch](https://medium.com/@SaschaKirch?source=post_page---byline--3b2660c35848--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3b2660c35848--------------------------------) ·20分钟阅读·2024年9月16日

--

![](../Images/7c83682cee1e25a2932aff0e19416df2.png)

图片来源：[Sascha Kirch](https://medium.com/@SaschaKirch).

> 这是我新系列文章[🐍 走向马姆巴状态空间模型：图像、视频与时间序列](https://medium.com/@SaschaKirch/list/mamba-state-space-models-for-images-videos-and-timeseries-861ae0ad08fb)的第4部分。

近年来，计算机视觉领域取得了令人难以置信的进展。推动这一发展的关键因素之一无疑是Transformer的引入。虽然Transformer彻底革新了自然语言处理，但我们花了几年时间才将其能力转移到视觉领域。可能最具代表性的论文是[Vision Transformer (ViT)](https://arxiv.org/abs/2010.11929)，这是一种模型，至今仍被许多现代架构作为基础模型使用。

正是Transformer的*O(L²)*复杂度限制了其在图像分辨率增高时的应用。借助于[马姆巴选择性状态空间模型](https://medium.com/towards-data-science/here-comes-mamba-the-selective-state-space-model-435e5d17a451?sk=602b692eda48c19b2b2f4b0a7198bbcb)，我们现在能够让历史重演，并将SSM（状态空间模型）在序列数据中的成功转移到非序列数据中：图像。

❗ 剧透提醒：VisionMamba比[DeiT](https://arxiv.org/abs/2012.12877)快2.8倍，并且在高分辨率图像（1248x1248）上节省了86.8%的GPU内存，在这篇文章中，你将看到如何做到…
