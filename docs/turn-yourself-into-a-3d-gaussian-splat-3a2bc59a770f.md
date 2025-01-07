# 将自己转化为 3D 高斯点绘

> 原文：[https://towardsdatascience.com/turn-yourself-into-a-3d-gaussian-splat-3a2bc59a770f?source=collection_archive---------6-----------------------#2024-03-14](https://towardsdatascience.com/turn-yourself-into-a-3d-gaussian-splat-3a2bc59a770f?source=collection_archive---------6-----------------------#2024-03-14)

## **实践者**的动手指南

[](https://medium.com/@SaschaKirch?source=post_page---byline--3a2bc59a770f--------------------------------)[![Sascha Kirch](../Images/a0d45da9dc9c602075b2810786c660c9.png)](https://medium.com/@SaschaKirch?source=post_page---byline--3a2bc59a770f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3a2bc59a770f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3a2bc59a770f--------------------------------) [Sascha Kirch](https://medium.com/@SaschaKirch?source=post_page---byline--3a2bc59a770f--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3a2bc59a770f--------------------------------) ·阅读时间 11 分钟·2024 年 3 月 14 日

--

去年夏天，一种非深度学习方法进入了新视图合成领域：[3D 高斯点绘](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/)。这是一种表示三维场景并能够从任意视角实时渲染图像的方法。有些人甚至认为它正在取代 NeRF，这一方法曾在当时主导新视图合成和隐式场景表示。我认为这点值得商榷，因为[NeRFs](https://www.matthewtancik.com/nerf)远不止是图像渲染器。但这些今天都不重要……今天我们只关心清晰的 3D 模型，而这正是 3D 高斯点绘所擅长的地方 🎉

在这篇文章中，我们将简要介绍高斯点绘，然后切换话题，我将展示如何将自己转化为一个 3D 模型。

**额外奖励**：最后我将展示如何将你的模型嵌入到任何网站的互动查看器中。

那么，我们开始吧！

![](../Images/f4d77321c2c5f433ba187b5e2e09f1b4.png)

图片来自 [Sascha Kirch](https://medium.com/@SaschaKirch).

# 大纲

1.  什么是高斯点绘？

1.  让我们将自己转化为 3D 高斯点绘

1.  结论与进一步资源

# 什么是高斯点绘？

3D 高斯点绘（Gaussian splatting）是一种表示三维场景的技术。它实际上只是众多方法之一。例如你……
