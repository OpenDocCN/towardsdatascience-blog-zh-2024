# 减少 Docker 镜像大小（大型语言模型，第 2 部分）

> 原文：[https://towardsdatascience.com/reducing-the-size-of-docker-images-serving-large-language-models-part-2-b7226a0b6514?source=collection_archive---------11-----------------------#2024-05-08](https://towardsdatascience.com/reducing-the-size-of-docker-images-serving-large-language-models-part-2-b7226a0b6514?source=collection_archive---------11-----------------------#2024-05-08)

## 如何再将“较小”的 Docker 镜像大小减少 10%

[](https://czuk.medium.com/?source=post_page---byline--b7226a0b6514--------------------------------)[![Michał Marcińczuk, Ph.D.](../Images/74fb7b0099084be3f7a35a149471ffbd.png)](https://czuk.medium.com/?source=post_page---byline--b7226a0b6514--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b7226a0b6514--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b7226a0b6514--------------------------------) [Michał Marcińczuk 博士](https://czuk.medium.com/?source=post_page---byline--b7226a0b6514--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b7226a0b6514--------------------------------) ·阅读时间 7 分钟·2024 年 5 月 8 日

--

![](../Images/627df8a3f69fdf970f463034b531d664.png)

由 Runway 为以下提示生成：船上有两个容器，一个大容器和一个小容器。它们的颜色明亮、鲜艳且真实。

# 介绍

这是关于减少用于大型语言模型的 Docker 镜像大小话题的延续。[在我之前的文章](/reducing-the-size-of-docker-images-serving-llm-models-b70ee66e5a76) [1]中，我展示了如何将 Docker 镜像模型的大小**从 7 GB 减少到 700 MB 以下**。该解决方案通过去除像 CUDA、cuDNN、cuBLAS、torch 和 triton 等重量级库来实现。通过将模型转换并量化为 ONNX 格式，并使用 CPU 版本的 `onnxruntime` 替代 GPU 版本的 `torch`，实现了这一目标。

在本文中，我将展示如何进一步将目标镜像的大小减少**10%**。这看起来可能有些过头，因为 700 MB 已经是一个相对较小的镜像。然而，本文介绍的技术可以更深入地了解服务语言模型的 Docker 镜像。它们有助于理解运行模型所需的组件，并发现可能存在一些更轻量的替代方案。

本文中使用的脚本和资源也可在 GitHub 上找到 [2]：
