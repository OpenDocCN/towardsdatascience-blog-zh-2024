# 扩散模型：Midjourney、Dall-E 通过反向时间从提示中生成图像

> 原文：[https://towardsdatascience.com/diffusion-models-midjourney-dall-e-reverse-time-to-generate-images-from-prompts-ba760f472103?source=collection_archive---------6-----------------------#2024-01-09](https://towardsdatascience.com/diffusion-models-midjourney-dall-e-reverse-time-to-generate-images-from-prompts-ba760f472103?source=collection_archive---------6-----------------------#2024-01-09)

[](https://medium.com/@rohitpandey576?source=post_page---byline--ba760f472103--------------------------------)[![Rohit Pandey](../Images/af817d8f68f2984058f0afb8fd7ecbe9.png)](https://medium.com/@rohitpandey576?source=post_page---byline--ba760f472103--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ba760f472103--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ba760f472103--------------------------------) [Rohit Pandey](https://medium.com/@rohitpandey576?source=post_page---byline--ba760f472103--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ba760f472103--------------------------------) ·18分钟阅读·2024年1月9日

--

![](../Images/5dc87e6fccc097baf368a326ff0cf3c5.png)

图片由作者提供。

如果你阅读过我最近的一些博客，你会知道我是新一代AI工具的重度用户，这些工具可以根据提示生成图像，属于最近的AI春天的一部分（我为使用Midjourney支付了月费）。

我对AI研究的一个看法是，它像是一场竞赛，研究人员在不断破解一些复杂的模型，直到它们能够推动某些基准变化，却没有花足够的时间去理解这些模型为什么会做出这样的行为。从这个角度看，这些模型的研究工作有时更像是一种艺术，而非科学。这也是我对深入研究这些模型产生轻微排斥的原因。而这种排斥感因开发速度的飞快而加剧。假如我花了大量时间去理解一个新奇的模型，它却在明天就被淘汰了，那该怎么办？

但最近图像生成模型的进展，让人只需输入一些图像描述，就能得到一张质量极高的作品（Midjourney、Dall-E 和开源的 Stable Diffusion 都是其中的代表），这让我不得不走出自己的“洞穴”并开始关注。[而关注正是我所需要的](https://arxiv.org/pdf/1706.03762.pdf)。

在这个过程中，我惊讶地发现，这些扩散模型背后的理论相当深奥，受到一门名为统计热力学的物理学分支的启发……
