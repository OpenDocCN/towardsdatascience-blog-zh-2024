# 艺术守护：保护你的在线图像免受生成式AI的侵害

> 原文：[https://towardsdatascience.com/art-guard-protecting-your-online-images-from-generative-ai-ef09b8aa1a5a?source=collection_archive---------9-----------------------#2024-08-23](https://towardsdatascience.com/art-guard-protecting-your-online-images-from-generative-ai-ef09b8aa1a5a?source=collection_archive---------9-----------------------#2024-08-23)

## 你可以采取的步骤，以防止机器人爬取并使用你的艺术作品训练AI模型，如 Stable Diffusion、Midjourney 和 DALL-E

[](https://robgon.medium.com/?source=post_page---byline--ef09b8aa1a5a--------------------------------)[![罗伯特·A·冈萨尔维斯](../Images/96b4da0f602a1cd9d1e1d2917868cbee.png)](https://robgon.medium.com/?source=post_page---byline--ef09b8aa1a5a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ef09b8aa1a5a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ef09b8aa1a5a--------------------------------) [罗伯特·A·冈萨尔维斯](https://robgon.medium.com/?source=post_page---byline--ef09b8aa1a5a--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ef09b8aa1a5a--------------------------------) ·18分钟阅读·2024年8月23日

--

![](../Images/21d726f224df803243928f48a318d9fe.png)

**艺术守护，DALL-E 3 生成的AI图像**，作者编辑

很多艺术家都对生成式AI感到担忧。他们担心网络爬虫未经许可和/或补偿，爬取他们网页上的图像用于训练AI模型。

我花了过去四周的时间研究这个话题，并找到了很多关于这些模型如何工作以及如何防止机器人窃取你作品的信息。

> TL;DR — 你可以做的最简单的事情是通过你的托管服务的设置关闭AI爬虫访问你的网站，比如 [SquareSpace](https://support.squarespace.com/hc/en-us/articles/360022347072-Request-that-AI-models-exclude-your-site) 和 [其他服务](https://www.foundationwebdev.com/2023/08/utilizing-robots-txt-to-block-ai-crawlers/)。更多关于这个和其他你可以采取的步骤的信息在下面。

在这篇文章中，我将提供一些关于文本生成图像的AI模型如何工作的背景知识，包括 Stable Diffusion、Midjourney 和 DALL-E 3。接下来，我将向你展示如何检测这些模型是否使用了你的图像进行训练。最后，我将提供一些建议和步骤，帮助你防止机器人窃取你的作品。
