# 17 种（高级）RAG 技术，将你的 LLM 应用程序原型转变为可投入生产的解决方案

> 原文：[`towardsdatascience.com/17-advanced-rag-techniques-to-turn-your-rag-app-prototype-into-a-production-ready-solution-5a048e36cdc8?source=collection_archive---------0-----------------------#2024-06-26`](https://towardsdatascience.com/17-advanced-rag-techniques-to-turn-your-rag-app-prototype-into-a-production-ready-solution-5a048e36cdc8?source=collection_archive---------0-----------------------#2024-06-26)

## 一系列 RAG 技术，帮助你将 RAG 应用程序开发成一个强大且持久的系统

[](https://dmnkplzr.medium.com/?source=post_page---byline--5a048e36cdc8--------------------------------)![Dominik Polzer](https://dmnkplzr.medium.com/?source=post_page---byline--5a048e36cdc8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5a048e36cdc8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5a048e36cdc8--------------------------------) [Dominik Polzer](https://dmnkplzr.medium.com/?source=post_page---byline--5a048e36cdc8--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5a048e36cdc8--------------------------------) ·阅读时间：24 分钟·2024 年 6 月 26 日

--

现在人们成为生成式 AI 专家的速度令人惊叹。每个人都宣称，生成式 AI 将带来下一次工业革命。

这是一个大承诺，但我同意，我认为这次是真的。AI 将最终彻底改变我们的工作和生活方式。我无法想象滑入下一个[AI 冬天](https://en.wikipedia.org/wiki/AI_winter)。

大型语言模型（LLMs）和多模态模型简直太有用了，而且“相对”容易集成到现有的流程中。一个向量数据库，几行代码来处理原始数据，再加上几次 API 调用。

> 就是这样。— 至少在理论上是这样。

尽管听起来相当直接，但行业中的真正进展可能更好地通过[Matt Turck 最近的一篇 LinkedIn 帖子来描述：](https://www.linkedin.com/posts/turck_2023-i-hope-generative-ai-doesnt-kill-activity-7193770162213302272-qqGd/?utm_source=share&utm_medium=member_desktop)

> 2023 年：“我希望生成式 AI 不会把我们全部杀掉！”
> 
> 2024 年：“我希望生成式 AI 能在我公司从概念验证实验，发展到小规模生产部署，在接下来的 12 到 18 个月内能读取 PDF！”
> 
> - Matt Turck
> 
> 构建一个原型很容易。将其转变为可投入生产的系统则非常困难。
