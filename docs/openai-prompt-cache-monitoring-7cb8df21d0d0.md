# OpenAI 提示缓存监控

> 原文：[https://towardsdatascience.com/openai-prompt-cache-monitoring-7cb8df21d0d0?source=collection_archive---------8-----------------------#2024-12-10](https://towardsdatascience.com/openai-prompt-cache-monitoring-7cb8df21d0d0?source=collection_archive---------8-----------------------#2024-12-10)

![](../Images/4346b5c75c12de574747e6ebd1229e71.png)

图像由 AI（Dalle-3）生成

## 使用 Python 和聊天完成 API 的工作示例

[](https://medium.com/@thomas_reid?source=post_page---byline--7cb8df21d0d0--------------------------------)[![Thomas Reid](../Images/c1b4e5f577272633ba07e5dbfd21c02d.png)](https://medium.com/@thomas_reid?source=post_page---byline--7cb8df21d0d0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7cb8df21d0d0--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7cb8df21d0d0--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--7cb8df21d0d0--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7cb8df21d0d0--------------------------------) ·阅读时长9分钟·2024年12月10日

--

作为他们最近 DEV Day 演示的一部分，OpenAI 宣布提示缓存现在已经适用于各种模型。写这篇文章时，这些模型包括：

> GPT-4o、GPT-4o mini、o1-preview和o1-mini，以及这些模型的微调版本。

这一消息不容小觑，因为它将帮助开发者节省成本并减少应用程序运行时延迟。

对支持的模型的 API 调用将在提示超过1,024个令牌时自动受益于提示缓存。API 会缓存已计算过的提示的最长前缀，从1,024个令牌开始，并按128个令牌的增量递增。如果你重用带有共同前缀的提示，OpenAI会自动应用提示缓存折扣，而无需你更改API集成。

作为 OpenAI API 开发者，你唯一可能需要担心的是如何监控你的提示缓存使用情况，即检查它是否已被应用。

在本文中，我将向你展示如何使用 Python、Jupyter Notebook 和一个聊天完成示例来实现这一点。

## 安装 WSL2 Ubuntu
