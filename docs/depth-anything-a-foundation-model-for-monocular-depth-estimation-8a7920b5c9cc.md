# Depth Anything — 单目深度估计的基础模型

> 原文：[https://towardsdatascience.com/depth-anything-a-foundation-model-for-monocular-depth-estimation-8a7920b5c9cc?source=collection_archive---------3-----------------------#2024-03-20](https://towardsdatascience.com/depth-anything-a-foundation-model-for-monocular-depth-estimation-8a7920b5c9cc?source=collection_archive---------3-----------------------#2024-03-20)

## [🚀Sascha的论文俱乐部](https://towardsdatascience.com/tagged/saschas-paper-club)

## Depth Anything：释放大规模未标记数据的力量，L. Yang等人

[](https://medium.com/@SaschaKirch?source=post_page---byline--8a7920b5c9cc--------------------------------)[![Sascha Kirch](../Images/a0d45da9dc9c602075b2810786c660c9.png)](https://medium.com/@SaschaKirch?source=post_page---byline--8a7920b5c9cc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8a7920b5c9cc--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8a7920b5c9cc--------------------------------) [Sascha Kirch](https://medium.com/@SaschaKirch?source=post_page---byline--8a7920b5c9cc--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8a7920b5c9cc--------------------------------) ·阅读时间：11分钟·2024年3月20日

--

![](../Images/072b25a65b15d2c83fdfe3a30967c559.png)

图片来自[Sascha Kirch](https://medium.com/@SaschaKirch)的[出版物](https://arxiv.org/abs/2401.10891)

单目深度估计，即从二维图像预测三维空间中的距离。正如几乎所有关于深度估计的论文所指出的那样，这个“病态且固有模糊的问题”是计算机视觉和机器人学中的一个基础性问题。同时，基础模型主导了基于深度学习的自然语言处理（NLP）和计算机视觉领域。若我们能够将它们的成功应用于深度估计，岂不是太棒了？

在今天的论文讲解中，我们将深入探讨Depth Anything，这是一种用于单目深度估计的基础模型。我们将了解它的架构、训练过程中使用的技巧以及它如何用于度量深度估计。

> **论文：** [Depth Anything: Unleashing the Power of Large-Scale Unlabeled Data](https://arxiv.org/abs/2401.10891)，Lihe Yang等，2024年1月19日
> 
> **资源：** [GitHub](https://github.com/LiheYoung/Depth-Anything) — [项目页面](https://depth-anything.github.io/) — [演示](https://huggingface.co/spaces/LiheYoung/Depth-Anything) — [检查点](https://huggingface.co/spaces/LiheYoung/Depth-Anything/tree/main)
> 
> **会议：** CVPR2024
> 
> **类别：** 基础模型，单目深度估计
> 
> [**其他讲解**](https://medium.com/@SaschaKirch/list/paper-walkthroughs-by-sascha-kirch-89c7847da8e2)**:**
> 
> [[BYOL](/byol-the-alternative-to-contrastive-self-supervised-learning-5d0a26983d7c?sk=fc5a3b3a556088181d8726226862252c)] — [[CLIP](/the-clip-foundation-model-7770858b487d?sk=a7b10ba1d0c3a20ecd4adb8200a48500)] — [[GLIP](/glip-introducing-language-image-pre-training-to-object-detection-5ddb601873aa?sk=4f0acb404a38d342b7669f861c013a05)] — [[Segment Anything](/segment-anything-promptable-segmentation-of-arbitrary-objects-f28958c5612d?sk=bd1311a6d8b1e0e6d3369d536dba0700)] — [[DINO](/segment-anything-promptable-segmentation-of-arbitrary-objects-f28958c5612d?sk=bd1311a6d8b1e0e6d3369d536dba0700)] — [[DDPM](/the-rise-of-diffusion-models-a-new-era-of-generative-deep-learning-3ef4779f6e1b?sk=8c178422a977c6f49ec24b13502be4fd)]
