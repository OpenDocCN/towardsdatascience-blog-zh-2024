# 精通 GenAI 机器学习系统设计面试（2）：设计 ChatGPT 记忆功能

> 原文：[`towardsdatascience.com/mastering-genai-ml-system-design-interview-2-design-chatgpt-memory-feature-fe908517d76c?source=collection_archive---------0-----------------------#2024-06-01`](https://towardsdatascience.com/mastering-genai-ml-system-design-interview-2-design-chatgpt-memory-feature-fe908517d76c?source=collection_archive---------0-----------------------#2024-06-01)

## 如何设计 ChatGPT 的跨会话记忆

[](https://medium.com/@jacklingenai?source=post_page---byline--fe908517d76c--------------------------------)![Jack Chih-Hsu Lin](https://medium.com/@jacklingenai?source=post_page---byline--fe908517d76c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fe908517d76c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fe908517d76c--------------------------------) [Jack Chih-Hsu Lin](https://medium.com/@jacklingenai?source=post_page---byline--fe908517d76c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fe908517d76c--------------------------------) ·16 分钟阅读·2024 年 6 月 1 日

--

![](img/7d3e32bdec1b4de8a154d406a5d13b38.png)

带有记忆的 AI（图片由作者使用 ChatGPT 制作）

> 2024 年 9 月 21 日备注：我正在与一家著名出版商合作编写《设计生成型 AI 系统》一书。请关注我的[Medium](https://medium.com/@jacklingenai)和[LinkedIn](https://www.linkedin.com/in/jacklingenai/)账号，获取最新更新。

# 背景/背景信息

我非常激动地得知，[ChatGPT 的记忆功能](https://openai.com/index/memory-and-new-controls-for-chatgpt/)于 2024 年 4 月 29 日发布，且仅限订阅用户使用。我迫不及待地尝试了这一功能。脑海中一直在思考这个记忆功能是如何实现的，以及其中涉及的重要考虑因素和权衡。通过遵循 GenAI 机器学习系统设计面试的原则和解决方案概述，我将在这篇文章中分享作为面试候选人设计 ChatGPT 记忆功能时的思考和考虑因素。

> 你能设计 ChatGPT 的记忆功能吗？

在我们尝试回答问题之前，让我们先来看看记忆功能的具体功能。

# ChatGPT 记忆功能

## 如何管理记忆
