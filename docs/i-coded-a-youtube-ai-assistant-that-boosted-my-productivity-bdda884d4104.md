# 我编写了一个 YouTube AI 助手，提升了我的工作效率

> 原文：[`towardsdatascience.com/i-coded-a-youtube-ai-assistant-that-boosted-my-productivity-bdda884d4104?source=collection_archive---------6-----------------------#2024-09-13`](https://towardsdatascience.com/i-coded-a-youtube-ai-assistant-that-boosted-my-productivity-bdda884d4104?source=collection_archive---------6-----------------------#2024-09-13)

![](img/4f2b6a2f2e26a705bb4c9f01b79e8f4e.png)

由作者使用 flux-dreamscape 通过 replicate 生成的图像。

## Python 的逐步教程

[](https://data-professor.medium.com/?source=post_page---byline--bdda884d4104--------------------------------)![Chanin Nantasenamat](https://data-professor.medium.com/?source=post_page---byline--bdda884d4104--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bdda884d4104--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bdda884d4104--------------------------------) [Chanin Nantasenamat](https://data-professor.medium.com/?source=post_page---byline--bdda884d4104--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bdda884d4104--------------------------------) ·7 分钟阅读·2024 年 9 月 13 日

--

你是否曾经发现自己需要浏览大量的 YouTube 视频来学习或研究某个特定的主题？观看数小时的视频、做笔记，但仍然忽略了重要的细节，这确实是一个挑战。

在本文中，我将介绍我是如何通过构建一个 Python 工作流来节省无数小时从 YouTube 视频中提取关键信息的。这个工作流利用了大型语言模型（LLMs）来回答关于视频内容的任何问题。这不仅让我节省了时间，还提升了我的生产力和学习效率。因此，我可以利用多出来的时间创作更多的内容或进行一次应得的休息。

让我带你了解一下我是如何创建这个 YouTube AI 助手的过程。让我们深入探讨吧！

# 我为什么要构建这个 YouTube AI 助手

在进入技术细节之前，让我们先探讨一下这个项目为什么对我来说是一个颠覆性的突破。

在我作为开发者倡导者的全职工作和作为 YouTuber 的兼职工作中（我经营着[数据教授](https://youtube.com/dataprofessor)YouTube 频道），我每天的核心工作之一就是内容研究。
