# 图像到图像的翻译与 FLUX.1：直觉与教程

> 原文：[`towardsdatascience.com/image-to-image-translation-with-flux-1-intuition-and-tutorial-001fc521ebe6?source=collection_archive---------6-----------------------#2024-10-14`](https://towardsdatascience.com/image-to-image-translation-with-flux-1-intuition-and-tutorial-001fc521ebe6?source=collection_archive---------6-----------------------#2024-10-14)

## 使用扩散模型基于现有图像生成新图像。

[](https://medium.com/@CVxTz?source=post_page---byline--001fc521ebe6--------------------------------)![Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--001fc521ebe6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--001fc521ebe6--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--001fc521ebe6--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--001fc521ebe6--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--001fc521ebe6--------------------------------) ·阅读时间：6 分钟·2024 年 10 月 14 日

--

![](img/bf188237fee67ca01548444336f0e896.png)

原图来源：[Sven Mieke](https://unsplash.com/@sxoxm?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)拍摄的照片，来源于[Unsplash](https://unsplash.com/photos/brown-tabby-cat-lying-on-white-textile-G-8B32scqMc?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) / 转换图像：Flux.1，提示词为“老虎的图片”

本文指导你如何基于现有图像和文本提示生成新图像。这项技术在名为[**SDEdit：基于随机微分方程的图像合成与编辑**](https://arxiv.org/abs/2108.01073)的论文中提出，现应用于 FLUX.1。

首先，我们将简要解释潜在扩散模型的工作原理。接下来，我们将看到 SDEdit 如何修改反向扩散过程，以便根据文本提示编辑图像。最后，我们将提供运行整个流程的代码。

# 背景：潜在扩散

潜在扩散在低维潜在空间中执行扩散过程。让我们定义潜在空间：

变分自编码器（VAE）将图像从像素空间（人类理解的 RGB 高度宽度表示）投影到一个较小的潜在空间。这种压缩保留了足够的信息，以便稍后重建图像。扩散过程在这个潜在空间中进行，因为它在计算上更便宜，并且对无关的像素空间细节不那么敏感。

## 现在，让我们解释潜在扩散：
