# 使用引导生成法构建 LLM 应用，节省时间和精力

> 原文：[https://towardsdatascience.com/save-time-and-effort-when-building-llm-apps-using-guided-generation-05f7237a3512?source=collection_archive---------13-----------------------#2024-07-29](https://towardsdatascience.com/save-time-and-effort-when-building-llm-apps-using-guided-generation-05f7237a3512?source=collection_archive---------13-----------------------#2024-07-29)

## 使用 Guidance 使 LLM 输出符合你的期望

[](https://medium.com/@CVxTz?source=post_page---byline--05f7237a3512--------------------------------)[![Youness Mansar](../Images/b68fe2cbbe219ab0231922c7165f2b6a.png)](https://medium.com/@CVxTz?source=post_page---byline--05f7237a3512--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--05f7237a3512--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--05f7237a3512--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--05f7237a3512--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--05f7237a3512--------------------------------) ·8分钟阅读·2024年7月29日

--

![](../Images/2dc86ebfea3eaee2537bd2de771e414e.png)

图片由 [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

大型语言模型（LLM）是强大的工具，但它们有时会表现得有些不可预测。有时，它们会给出错误的答案，其他时候，它们的回答格式完全不对。这看起来可能不是什么大问题，但当你用 LLM 来分析数据、分类信息，或与需要特定结构的其他工具协作时，确保格式正确是至关重要的。

你可以通过巧妙的提示和示例来尝试引导 LLM 朝正确的方向发展，但即便是这些方法也不是万无一失的。一个更极端的解决方案是通过大量按你要求格式化的数据来微调 LLM。虽然有效，但这种方法可能会消耗大量资源。

那么，解决的办法是什么呢？引导生成！这一技术允许你在不需要重新训练的情况下，通过引导来影响 LLM 的输出，将其约束到所需的格式。在这篇文章中，我们将探讨微软的“Guidance”库，这是最受欢迎的引导生成工具之一，并了解它如何帮你节省时间，使 LLM 的交互更加可预测。我们将探讨几个实际应用，包括：

+   **文本分类**：自动将文本分类到预定义的组别中。
