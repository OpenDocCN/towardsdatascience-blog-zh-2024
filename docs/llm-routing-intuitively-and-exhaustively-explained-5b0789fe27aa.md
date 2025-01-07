# LLM 路由 — 直观且详尽的解释

> 原文：[`towardsdatascience.com/llm-routing-intuitively-and-exhaustively-explained-5b0789fe27aa?source=collection_archive---------0-----------------------#2024-11-22`](https://towardsdatascience.com/llm-routing-intuitively-and-exhaustively-explained-5b0789fe27aa?source=collection_archive---------0-----------------------#2024-11-22)

## 在每个查询上动态选择合适的语言模型

[](https://medium.com/@danielwarfield1?source=post_page---byline--5b0789fe27aa--------------------------------)![Daniel Warfield](https://medium.com/@danielwarfield1?source=post_page---byline--5b0789fe27aa--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5b0789fe27aa--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5b0789fe27aa--------------------------------) [Daniel Warfield](https://medium.com/@danielwarfield1?source=post_page---byline--5b0789fe27aa--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5b0789fe27aa--------------------------------) ·阅读时间 49 分钟·2024 年 11 月 22 日

--

![](img/f2a70c84e7930d40d1b4dcfeca757f73.png)

“和谐”，由 Daniel Warfield 使用 Midjourney 创作。除非另有说明，所有图片均由作者提供。本文最初发布于 [直观且详尽的解释](https://iaee.substack.com/)。

在这篇文章中，我们将讨论“LLM 路由”，这是一种高级推理技术，可以自动从一组语言模型中选择最适合给定提示的语言模型；提高 LLM 驱动系统的性能、速度和成本效益。

我们将探索四种 LLM 路由方法：三种来自学术界，一种来自行业，以全面理解这一概念和技术。在此过程中，我们将探讨多种建模策略，这些策略在重要的 AI 应用场景中非常有用，比如自我评估、自治系统和在不确定性面前的决策制定。

**这对谁有用？** 任何想要深入理解人工智能以及构建前沿 AI 驱动系统所需核心方法的人。

**这篇文章有多高级？** 文章的前面部分适合各个层次的读者阅读。后面的部分则更倾向于有一定经验的数据科学家和开发者。

**前提条件：** 早期部分适合所有层次的读者，但后面的部分包含一些可能对经验较少的读者来说至关重要的支持内容。
