# 17种（高级）RAG技术，将你的LLM应用程序原型转变为可投入生产的解决方案

> 原文：[https://towardsdatascience.com/17-advanced-rag-techniques-to-turn-your-rag-app-prototype-into-a-production-ready-solution-5a048e36cdc8?source=collection_archive---------0-----------------------#2024-06-26](https://towardsdatascience.com/17-advanced-rag-techniques-to-turn-your-rag-app-prototype-into-a-production-ready-solution-5a048e36cdc8?source=collection_archive---------0-----------------------#2024-06-26)

## 一系列RAG技术，帮助你将RAG应用程序开发成一个强大且持久的系统

[](https://dmnkplzr.medium.com/?source=post_page---byline--5a048e36cdc8--------------------------------)[![Dominik Polzer](../Images/7e48cd15df31a0ab961391c0d57521de.png)](https://dmnkplzr.medium.com/?source=post_page---byline--5a048e36cdc8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5a048e36cdc8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5a048e36cdc8--------------------------------) [Dominik Polzer](https://dmnkplzr.medium.com/?source=post_page---byline--5a048e36cdc8--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5a048e36cdc8--------------------------------) ·阅读时间：24分钟·2024年6月26日

--

现在人们成为生成式AI专家的速度令人惊叹。每个人都宣称，生成式AI将带来下一次工业革命。

这是一个大承诺，但我同意，我认为这次是真的。AI将最终彻底改变我们的工作和生活方式。我无法想象滑入下一个[AI冬天](https://en.wikipedia.org/wiki/AI_winter)。

大型语言模型（LLMs）和多模态模型简直太有用了，而且“相对”容易集成到现有的流程中。一个向量数据库，几行代码来处理原始数据，再加上几次API调用。

> 就是这样。— 至少在理论上是这样。

尽管听起来相当直接，但行业中的真正进展可能更好地通过[Matt Turck最近的一篇LinkedIn帖子来描述：](https://www.linkedin.com/posts/turck_2023-i-hope-generative-ai-doesnt-kill-activity-7193770162213302272-qqGd/?utm_source=share&utm_medium=member_desktop)

> 2023年：“我希望生成式AI不会把我们全部杀掉！”
> 
> 2024年：“我希望生成式AI能在我公司从概念验证实验，发展到小规模生产部署，在接下来的12到18个月内能读取PDF！”
> 
> - Matt Turck
> 
> 构建一个原型很容易。将其转变为可投入生产的系统则非常困难。
