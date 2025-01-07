# 为 GitHub 构建 LLM 驱动的编码助手：使用 Gemini 和 Redis 的 RAG

> 原文：[`towardsdatascience.com/building-llm-powered-coding-assitant-for-github-rag-with-gemini-and-redis-b88beeb42f2d?source=collection_archive---------6-----------------------#2024-08-12`](https://towardsdatascience.com/building-llm-powered-coding-assitant-for-github-rag-with-gemini-and-redis-b88beeb42f2d?source=collection_archive---------6-----------------------#2024-08-12)

## 如何构建一个能够回答用户问题的 GitHub 仓库助手

[](https://ransakaravihara.medium.com/?source=post_page---byline--b88beeb42f2d--------------------------------)![Ransaka Ravihara](https://ransakaravihara.medium.com/?source=post_page---byline--b88beeb42f2d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b88beeb42f2d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b88beeb42f2d--------------------------------) [Ransaka Ravihara](https://ransakaravihara.medium.com/?source=post_page---byline--b88beeb42f2d--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b88beeb42f2d--------------------------------) ·阅读时间 8 分钟·2024 年 8 月 12 日

--

![](img/f2c09a2a963fe4d4478bf662bdb2e732.png)

一个热衷于探索并帮助他人的极客。（使用[Canva](https://www.canva.com/ai-image-generator/)生成）

# 介绍

既然你正在阅读这篇文章，说明你可能对数据科学、机器学习或人工智能感兴趣；最终，你的目标是编程。程序员更常遇到的是 Bug、问题和错误。这时问题变得严肃，尤其是在处理一个相对较新的框架或库时。此时，我总是想到拥有一位智能助手的好处，那个助手掌握了各种知识，并且能够有效地提供指导。我的助手一旦接到任务，就可以浏览整个代码库，扫描每一行代码，记住所有的逻辑，迅速修复问题。

![](img/8716cb3a3bbd8b836f15ac4f3ad84b6b.png)

图片由作者提供，通过[Canva](https://www.canva.com/ai-image-generator/)

听起来很高大上，对吧？创建这样一个系统曾经看起来像是科幻小说中的情节，但如今它是可以实现的，只需要几个专注的晚上。

让我们准备好并开始编码，来实现我们的任务。如上所述，我们的助手具有以下功能：
