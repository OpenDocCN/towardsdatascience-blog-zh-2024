# 高级代码生成与大语言模型 — 构建一个合成数据生成器

> 原文：[`towardsdatascience.com/advanced-code-generation-with-llms-building-a-synthetic-data-generator-0e7ed7b496dc?source=collection_archive---------4-----------------------#2024-04-14`](https://towardsdatascience.com/advanced-code-generation-with-llms-building-a-synthetic-data-generator-0e7ed7b496dc?source=collection_archive---------4-----------------------#2024-04-14)

## 应用 INSPIRe 框架的六个步骤来加速你的代码生成（ChatGPT-4 — Claude 3 — Gemini）

[](https://nabil-alouani.medium.com/?source=post_page---byline--0e7ed7b496dc--------------------------------)![Nabil Alouani](https://nabil-alouani.medium.com/?source=post_page---byline--0e7ed7b496dc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0e7ed7b496dc--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0e7ed7b496dc--------------------------------) [Nabil Alouani](https://nabil-alouani.medium.com/?source=post_page---byline--0e7ed7b496dc--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0e7ed7b496dc--------------------------------) ·31 分钟阅读·2024 年 4 月 14 日

--

![](img/798531da216cd7dd21305d6d865eb150.png)

由作者构思的图像。

我从未从头到尾写过一个数据科学项目。但在 Jupyter Notebook 中你能做的任何事情，我也能做到。是的，真的，*任何*事情。

夸耀归夸耀，这是 AI 在过去两年为我们带来的最重要的变革之一。大语言模型将代码变成了商品；你可以把它当做工具使用，而不是一项技能。

你只需要五个元素：

1.  数据素养。

1.  一点逻辑。

1.  一种试错的天赋。

1.  提示工程。

1.  INSPIRe 框架。

如果你在想 INSPIRe 到底是什么，应该先读一下[本系列的第一部分](https://medium.com/@nabil-alouani/6-practical-steps-to-make-ai-write-your-python-code-for-you-2b3c6a35f174)。你将会了解该框架的介绍以及如何应用它。不过，如果你感觉冒险，接下来的简短回顾应该足够了。

[`medium.com/@nabil-alouani/6-practical-steps-to-make-ai-write-your-python-code-for-you-2b3c6a35f174`](https://medium.com/@nabil-alouani/6-practical-steps-to-make-ai-write-your-python-code-for-you-2b3c6a35f174)
