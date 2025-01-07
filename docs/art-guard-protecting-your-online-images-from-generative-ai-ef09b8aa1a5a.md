# 艺术守护：保护你的在线图像免受生成式 AI 的侵害

> 原文：[`towardsdatascience.com/art-guard-protecting-your-online-images-from-generative-ai-ef09b8aa1a5a?source=collection_archive---------9-----------------------#2024-08-23`](https://towardsdatascience.com/art-guard-protecting-your-online-images-from-generative-ai-ef09b8aa1a5a?source=collection_archive---------9-----------------------#2024-08-23)

## 你可以采取的步骤，以防止机器人爬取并使用你的艺术作品训练 AI 模型，如 Stable Diffusion、Midjourney 和 DALL-E

[](https://robgon.medium.com/?source=post_page---byline--ef09b8aa1a5a--------------------------------)![罗伯特·A·冈萨尔维斯](https://robgon.medium.com/?source=post_page---byline--ef09b8aa1a5a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ef09b8aa1a5a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ef09b8aa1a5a--------------------------------) [罗伯特·A·冈萨尔维斯](https://robgon.medium.com/?source=post_page---byline--ef09b8aa1a5a--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ef09b8aa1a5a--------------------------------) ·18 分钟阅读·2024 年 8 月 23 日

--

![](img/21d726f224df803243928f48a318d9fe.png)

**艺术守护，DALL-E 3 生成的 AI 图像**，作者编辑

很多艺术家都对生成式 AI 感到担忧。他们担心网络爬虫未经许可和/或补偿，爬取他们网页上的图像用于训练 AI 模型。

我花了过去四周的时间研究这个话题，并找到了很多关于这些模型如何工作以及如何防止机器人窃取你作品的信息。

> TL;DR — 你可以做的最简单的事情是通过你的托管服务的设置关闭 AI 爬虫访问你的网站，比如 [SquareSpace](https://support.squarespace.com/hc/en-us/articles/360022347072-Request-that-AI-models-exclude-your-site) 和 [其他服务](https://www.foundationwebdev.com/2023/08/utilizing-robots-txt-to-block-ai-crawlers/)。更多关于这个和其他你可以采取的步骤的信息在下面。

在这篇文章中，我将提供一些关于文本生成图像的 AI 模型如何工作的背景知识，包括 Stable Diffusion、Midjourney 和 DALL-E 3。接下来，我将向你展示如何检测这些模型是否使用了你的图像进行训练。最后，我将提供一些建议和步骤，帮助你防止机器人窃取你的作品。
