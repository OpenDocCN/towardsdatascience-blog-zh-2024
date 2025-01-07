# 构建 WhatsApp LLM 机器人：懒人单人程序员指南

> 原文：[`towardsdatascience.com/build-a-whatsapp-llm-bot-a-guide-for-lazy-solo-programmers-24934d8f5488?source=collection_archive---------3-----------------------#2024-09-20`](https://towardsdatascience.com/build-a-whatsapp-llm-bot-a-guide-for-lazy-solo-programmers-24934d8f5488?source=collection_archive---------3-----------------------#2024-09-20)

## 我如何在 12 小时内使用 Python、AWS 和 OpenAI 构建它，并总结经验教训

[](https://medium.com/@ianxiao?source=post_page---byline--24934d8f5488--------------------------------)![Ian Xiao](https://medium.com/@ianxiao?source=post_page---byline--24934d8f5488--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--24934d8f5488--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--24934d8f5488--------------------------------) [Ian Xiao](https://medium.com/@ianxiao?source=post_page---byline--24934d8f5488--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--24934d8f5488--------------------------------) ·阅读时长：8 分钟·2024 年 9 月 20 日

--

![](img/6deaee4817f5c361e3cf094d12f7735e.png)

图片来源：[Milad Fakurian](https://unsplash.com/@fakurian?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在 [Unsplash](https://unsplash.com/photos/a-blue-and-white-abstract-design-on-a-black-background-4KYpp7fR5Ew?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 上

**简而言之：** 我在 12 小时内构建并部署了一个 WhatsApp LLM 机器人，以便更快、更好地学习英语。我正在探索如何将 LLM 应用于我们的日常生活。我分享了我的设计选择、构建的内容、使用的工具、经验教训以及产品路线图。

我正在分阶段构建这个应用。敬请关注后续更新。

👉 看起来对你有用吗？**请花 3 分钟完成这个** [**调查**](https://forms.gle/nZtEjN8bPd4mfks29)**。** 我需要来自社区的一些设计指导，希望你能参与测试版。

*这不是一篇代码讲解。我将在最后列出我所使用的所有资源，如果你感兴趣，可以查看。*

## 问题

我喜欢阅读和写作。

但是，作为一名非英语母语者，我经常会遇到一些我不懂的新词，或者是认为自己懂的词，但实际上需要帮助理解。新词总是匆匆而过，在我忙碌的日常或享受阅读的过程中。我希望它们能记住；我希望自己变得更有口才。

怎么样，查找并记录它们呢？数字解决方案（如词典或词汇应用）和纸笔并不起作用。
