# ChatGPT 与 Claude 与 Gemini 在数据分析中的比较（第一部分）

> 原文：[https://towardsdatascience.com/chatgpt-vs-claude-vs-gemini-for-data-analysis-part-1-821086810318?source=collection_archive---------0-----------------------#2024-08-05](https://towardsdatascience.com/chatgpt-vs-claude-vs-gemini-for-data-analysis-part-1-821086810318?source=collection_archive---------0-----------------------#2024-08-05)

## 十个问题，用来测试哪个 AI 助手写的 SQL 最好

[](https://ydong029.medium.com/?source=post_page---byline--821086810318--------------------------------)[![Yu Dong](../Images/55c3c11c76cde72c65eb81a60384a436.png)](https://ydong029.medium.com/?source=post_page---byline--821086810318--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--821086810318--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--821086810318--------------------------------) [Yu Dong](https://ydong029.medium.com/?source=post_page---byline--821086810318--------------------------------)

· 发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--821086810318--------------------------------) ·阅读时间：18 分钟 ·2024 年 8 月 5 日

--

# 目录

· [上下文](#b483)

· [让我们比较它们的 SQL 技能！](#f39c)

· [第一回合：问题解决（LeetCode SQL）](#c188)

· [第二回合：商业逻辑](#7c22)

· [第三回合：查询优化](#d1b9)

· [总结](#6c1e)

· [接下来是什么](#b6fb)

# 上下文

欢迎来到我新系列的第一篇文章，*ChatGPT 与 Claude 与 Gemini 在数据分析中的比较*。在这个系列中，我将比较这些 AI 模型在各种数据科学和分析任务中的表现，旨在帮助数据爱好者和专业人士选择最适合他们需求的 AI 助手。

以下是我将要比较的具体模型。我选择这三款是因为它们都提供了一个易于访问的聊天机器人界面，非常适合日常任务。我使用了我的订阅账户进行对比（是的，我上周订阅了 Claude 和 Gemini 高级版为这篇文章做对比 😂）。

**ChatGPT 4o：** 提供给所有 [ChatGPT](http://chatgpt.com) 用户，但免费用户的使用限制较低。Plus 订阅费用为每月 $20。

+   上下文窗口：128k 个令牌

+   训练数据截至 2023 年 10 月（[来源](https://platform.openai.com/docs/models/gpt-4o)）
