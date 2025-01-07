# 以AI方式应对认知失调

> 原文：[https://towardsdatascience.com/dealing-with-cognitive-dissonance-the-ai-way-1f182a248d6d?source=collection_archive---------8-----------------------#2024-07-04](https://towardsdatascience.com/dealing-with-cognitive-dissonance-the-ai-way-1f182a248d6d?source=collection_archive---------8-----------------------#2024-07-04)

## 语言模型如何处理提示中的冲突指令？

[](https://medium.com/@artfish?source=post_page---byline--1f182a248d6d--------------------------------)[![Yennie Jun](../Images/b635e965f21c3d55833269e12e861322.png)](https://medium.com/@artfish?source=post_page---byline--1f182a248d6d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1f182a248d6d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1f182a248d6d--------------------------------) [Yennie Jun](https://medium.com/@artfish?source=post_page---byline--1f182a248d6d--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1f182a248d6d--------------------------------) ·阅读时间7分钟·2024年7月4日

--

![](../Images/e1ca5f7c001c7c547024d3657e166b64.png)

在系统消息、提示和示例中给出矛盾的指令时，LLM会遵循哪条指令来生成回答？由作者创作。

*转载于* [*Art Fish Intelligence*](https://www.artfish.ai/p/dealing-with-cognitive-dissonance)*.*

语言模型如何处理提示中的冲突指令？

[认知失调](https://en.wikipedia.org/wiki/Cognitive_dissonance)是一个心理学术语，用来描述个体在持有两个或多个矛盾信念时所经历的心理不适。例如，如果你在杂货店看到一个标明“10件物品或更少”的结账通道，但排队的每个人都有10件或更多物品，那么你应该怎么办？

在AI的背景下，我想了解大型语言模型（LLM）如何处理以矛盾指令形式呈现的认知失调（例如，提示LLM从英语翻译成韩语，但却给出英语到法语的翻译示例）。

在这篇文章中，我通过给LLM提供矛盾信息来进行实验，以确定LLM更可能与哪些矛盾信息保持一致。

# 系统消息、提示指令和少量示例

作为用户，你可以通过以下三种方式之一告诉LLM该做什么：

+   直接在系统消息中描述任务
