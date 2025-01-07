# 如何从 LLM 获取 JSON 输出：实用指南

> 原文：[`towardsdatascience.com/how-to-get-json-output-from-llms-a-practical-guide-838234ba3bab?source=collection_archive---------1-----------------------#2024-08-16`](https://towardsdatascience.com/how-to-get-json-output-from-llms-a-practical-guide-838234ba3bab?source=collection_archive---------1-----------------------#2024-08-16)

## 使用 Llama.cpp 或 Gemini API 强制 JSON 输出的教程

[](https://medium.com/@CVxTz?source=post_page---byline--838234ba3bab--------------------------------)![Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--838234ba3bab--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--838234ba3bab--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--838234ba3bab--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--838234ba3bab--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--838234ba3bab--------------------------------) ·6 分钟阅读·2024 年 8 月 16 日

--

![](img/4f4a4ebe1e72bb0b28eee0286a787add.png)

图片来自 [Etienne Girardet](https://unsplash.com/@etiennegirardet?utm_source=medium&utm_medium=referral) 由 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 提供

大型语言模型（LLMs）擅长生成文本，但获得像 JSON 这样的结构化输出通常需要巧妙的提示，并希望 LLM 能理解。幸运的是，**JSON 模式**在 LLM 框架和服务中变得越来越普遍。这使你可以定义你想要的确切输出模式。

本文讨论了使用 JSON 模式进行约束生成。我们将使用一个复杂的、嵌套的、真实的 JSON 模式示例来引导 LLM 框架/APIs（如 Llama.cpp 或 Gemini API）生成结构化数据，特别是旅游地点信息。这建立在之前关于使用 **Guidance** 进行约束生成的帖子基础上，但重点关注更广泛采用的 JSON 模式。

[](/save-time-and-effort-when-building-llm-apps-using-guided-generation-05f7237a3512?source=post_page-----838234ba3bab--------------------------------) ## 使用引导生成节省构建 LLM 应用的时间和精力

### 使用 Guidance 使 LLM 输出符合你的期望

[towardsdatascience.com

尽管比 **Guidance** 更有限，但 JSON 模式的更广泛支持使其更加易于访问，尤其是在基于云的 LLM 提供商中。

在个人项目中，我发现虽然使用 Llama.cpp 时 JSON 模式非常直接，但要使其…
