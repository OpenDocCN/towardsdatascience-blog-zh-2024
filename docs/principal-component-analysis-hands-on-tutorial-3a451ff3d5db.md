# 主成分分析 — 实操教程

> 原文：[https://towardsdatascience.com/principal-component-analysis-hands-on-tutorial-3a451ff3d5db?source=collection_archive---------1-----------------------#2024-09-18](https://towardsdatascience.com/principal-component-analysis-hands-on-tutorial-3a451ff3d5db?source=collection_archive---------1-----------------------#2024-09-18)

## 通过主成分分析（PCA）进行降维。

[](https://medium.com/@fmnobar?source=post_page---byline--3a451ff3d5db--------------------------------)[![Farzad Nobar](../Images/2d75209693b712300e6f0796bd2487d0.png)](https://medium.com/@fmnobar?source=post_page---byline--3a451ff3d5db--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3a451ff3d5db--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3a451ff3d5db--------------------------------) [Farzad Nobar](https://medium.com/@fmnobar?source=post_page---byline--3a451ff3d5db--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3a451ff3d5db--------------------------------) ·12分钟阅读·2024年9月18日

--

![](../Images/ad93d4b5615b1c2eeae8d1facf80c77d.png)

图片由 [carlos lugo](https://unsplash.com/@carlos95lugo?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来源于 [Unsplash](https://unsplash.com/photos/two-scoops-of-ice-cream-in-a-glass-on-a-marble-table-cZEL2nI-R7I?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

主成分分析（PCA）是统计学家和机器学习实践者使用的最流行的降维方法之一。在深入了解这一点之前，让我们先讨论一些我们日常生活中使用此类方法的情景，可能我们甚至没有意识到。

1.  **搜索引擎：** 当我们使用 Google 或其他网站来寻找问题答案时，搜索引擎并不是逐字匹配我们的搜索查询，而是采用一些方法，首先将我们的搜索内容降维为更小的部分，然后再进行搜索——降维后复杂度减少，从而获得更快的结果。

1.  **图像压缩：** 你还记得曾经尝试上传图片到网站时，结果发现图片超过了最大文件大小的情形吗？在处理了随之而来的沮丧后，我们通常会寻求如 Photoshop 等工具的帮助，来减少图片的大小。 Photoshop 或类似工具在背后进行的操作叫做降维。

1.  **音乐：** 音乐流媒体服务使用各种方法来减少正在流式传输的音乐大小，以便节省我们和…
