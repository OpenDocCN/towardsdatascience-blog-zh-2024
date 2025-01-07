# 从数据到可视化：使用 OpenAI 助理 API 和 GPT-4o

> 原文：[`towardsdatascience.com/from-data-to-visualization-with-the-openai-assistants-api-and-gpt-4o-69af0cac5118?source=collection_archive---------5-----------------------#2024-06-12`](https://towardsdatascience.com/from-data-to-visualization-with-the-openai-assistants-api-and-gpt-4o-69af0cac5118?source=collection_archive---------5-----------------------#2024-06-12)

## 我们将探索 OpenAI 助理 API 中的代码补全工具，直接从数据创建可视化图表

[](https://medium.com/@alan-jones?source=post_page---byline--69af0cac5118--------------------------------)![Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--69af0cac5118--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--69af0cac5118--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--69af0cac5118--------------------------------) [Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--69af0cac5118--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--69af0cac5118--------------------------------) ·阅读时间：13 分钟·2024 年 6 月 12 日

--

![](img/0d88c75d5a2e01843c0c621aaff8f8d7.png)

**编程工具** — 图片由 [Quaritsch Photography](https://unsplash.com/@quaritsch?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 和 [Anton Savinov](https://unsplash.com/@tonchik?utm_source=medium&utm_medium=referral) 提供，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

随着 GPT-4 能力的不断扩展，基于其技术构建的 OpenAI 工具正在不断演变，成为开发者越来越强大的资产。

在本文中，我们将探索最新版本的图表制作功能。我们将为助理提供一个数据文件和具体指令，看看它如何将我们的数据可视化构思变为现实。

我们将使用助理 API 中内置的工具来实现这一目标。

目前，在 OpenAI Python 包（写作时为 v1.30.0）中，助理 API 包含工具：文件搜索、代码补全和函数调用。

函数调用允许开发者定义 AI 可以智能选择的函数，以完成任务；文件搜索允许开发者上传各种文件类型，这些文件可以以 RAG 风格存储在向量数据库中；代码补全让助理能够在沙盒环境中编写并运行 Python 程序，以解决编程和数学问题。
