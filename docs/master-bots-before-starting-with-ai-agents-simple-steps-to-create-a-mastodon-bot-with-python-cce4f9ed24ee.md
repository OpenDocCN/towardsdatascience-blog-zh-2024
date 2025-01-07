# 在开始使用 AI 代理之前掌握机器人：使用 Python 创建 Mastodon 机器人简单步骤

> 原文：[`towardsdatascience.com/master-bots-before-starting-with-ai-agents-simple-steps-to-create-a-mastodon-bot-with-python-cce4f9ed24ee?source=collection_archive---------2-----------------------#2024-12-27`](https://towardsdatascience.com/master-bots-before-starting-with-ai-agents-simple-steps-to-create-a-mastodon-bot-with-python-cce4f9ed24ee?source=collection_archive---------2-----------------------#2024-12-27)

[](https://medium.com/@schuerch_sarah?source=post_page---byline--cce4f9ed24ee--------------------------------)![Sarah Lea](https://medium.com/@schuerch_sarah?source=post_page---byline--cce4f9ed24ee--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cce4f9ed24ee--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cce4f9ed24ee--------------------------------) [Sarah Lea](https://medium.com/@schuerch_sarah?source=post_page---byline--cce4f9ed24ee--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cce4f9ed24ee--------------------------------) ·阅读时间 11 分钟·2024 年 12 月 27 日

--

我最近在 Mastodon 上发布了一条帖子，在两分钟内被其他六个账户分享。出于好奇，我访问了这些账户的资料，发现至少其中一个是技术机器人——这些账户会根据标签（例如 #datascience 或 #opensource）自动分享帖子。

Mastodon 目前正在快速增长，作为 X（前身为 Twitter）的去中心化替代品。像这样的平台上的机器人如何使我们的日常生活更轻松？它们又有哪些风险？机器人是丰富还是破坏了社交网络？我该如何使用 Mastodon API 来自己创建一个机器人？

在本文中，我不仅会向你展示机器人如何工作，还会提供一个逐步的指南，带有代码示例和截图，教你如何使用 Python 创建一个 Mastodon 机器人并使用其 API。

> **目录**
> 
> 1 — 为什么 Mastodon 和技术机器人存在？
> 
> 2 — 社交网络上的机器人技术基础
> 
> 3 — 机器人：效益与风险之间的平衡
> 
> 4 — 如何创建一个 Mastodon 机器人：逐步 Python 指南
> 
> 最后思考

# 1 — 为什么 Mastodon 和技术机器人存在？

Mastodon 是由 Eugen Rochko 于 2016 年在德国开发的去中心化社交网络。该平台是开源的，基于一个服务器网络，多个服务器共同组成了所谓的“联邦宇宙”（Fediverse）。如果你想分享帖子，可以选择一个服务器，例如 mastodon.social 或…
