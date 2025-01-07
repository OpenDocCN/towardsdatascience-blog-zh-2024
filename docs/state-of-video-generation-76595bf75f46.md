# 视频生成的现状

> 原文：[https://towardsdatascience.com/state-of-video-generation-76595bf75f46?source=collection_archive---------10-----------------------#2024-02-16](https://towardsdatascience.com/state-of-video-generation-76595bf75f46?source=collection_archive---------10-----------------------#2024-02-16)

## 视频生成领域的最新发展概述：从大语言模型（LLMs）到多重扩散（MultiDiffusion）。演变与关键问题。

[](https://medium.com/@nikita_kiselov?source=post_page---byline--76595bf75f46--------------------------------)[![Nikita Kiselov](../Images/7a9cca0418a58e35e1024d59a8c634bf.png)](https://medium.com/@nikita_kiselov?source=post_page---byline--76595bf75f46--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--76595bf75f46--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--76595bf75f46--------------------------------) [Nikita Kiselov](https://medium.com/@nikita_kiselov?source=post_page---byline--76595bf75f46--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--76595bf75f46--------------------------------) ·16分钟阅读·2024年2月16日

--

![](../Images/954d8538b1b69e49be5232a001a4e25f.png)

在Runway Gen2中生成的动画 | 录像机的缩小镜头

虽然2023年是大语言模型（LLMs）和图像生成技术爆发的一年，但视频生成技术却相对较少受到关注。在研究这一话题时，我发现很难跟上最新的发展和整体架构设计，因为它们代表了各种各样的模型。

在这篇文章中，我旨在分享近年来视频生成技术的发展，模型架构的演变，以及我们现在面临的关键问题。

> 在写这篇文章时，OpenAI发布了Sora——一款功能惊人的视频生成模型。尽管其架构尚未公开，但我希望你能从中获得一些洞察，了解它可能的构建方式。

# 让我们深入探讨时间线
