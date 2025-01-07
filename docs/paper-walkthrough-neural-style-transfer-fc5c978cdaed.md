# 论文解读：神经风格迁移

> 原文：[https://towardsdatascience.com/paper-walkthrough-neural-style-transfer-fc5c978cdaed?source=collection_archive---------7-----------------------#2024-12-03](https://towardsdatascience.com/paper-walkthrough-neural-style-transfer-fc5c978cdaed?source=collection_archive---------7-----------------------#2024-12-03)

## 使用深度学习将您的照片变成画作——从零开始实现NST，使用PyTorch

[](https://medium.com/@muhammad_ardi?source=post_page---byline--fc5c978cdaed--------------------------------)[![穆罕默德·阿尔迪](../Images/b59b3752bc33df0166eea834bbdb122f.png)](https://medium.com/@muhammad_ardi?source=post_page---byline--fc5c978cdaed--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fc5c978cdaed--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fc5c978cdaed--------------------------------) [穆罕默德·阿尔迪](https://medium.com/@muhammad_ardi?source=post_page---byline--fc5c978cdaed--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fc5c978cdaed--------------------------------) ·阅读时长：23分钟·2024年12月3日

--

![](../Images/d404922259dbb1a0da3f71218844a275.png)

图片由[Birmingham Museums Trust](https://unsplash.com/@birminghammuseumstrust?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍

最近，“生成式AI”成为全球热议的话题，这得益于像ChatGPT、Gemini、Claude等公开发布的AI模型。正如我们所知，这些模型最初只能理解和生成文本，但不久之后，它们也获得了对图像执行相同操作的能力。更具体地说，关于图像数据的生成模型，实际上我们可以使用很多不同的模型变体，每个模型都有其特定的目的。到目前为止，我已经在Medium上发布了关于图像数据生成AI的一些文章，比如自动编码器和变分自动编码器（VAE），这些文章我会在本文末尾附上链接。在今天的文章中，我将介绍另一种令人着迷的生成算法：神经风格迁移（NST）。

NST首次出现在2015年Gatys等人撰写的论文《*A Neural Algorithm of Artistic Style*》中[1]。论文中解释了他们的主要目标是将一幅图像（通常是一幅画）的艺术风格转移到另一幅图像上，这也是“风格迁移”名称的由来。请看…
