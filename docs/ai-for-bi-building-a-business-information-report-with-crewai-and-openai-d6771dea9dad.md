# AI与BI：使用CrewAI和OpenAI构建商业信息报告

> 原文：[https://towardsdatascience.com/ai-for-bi-building-a-business-information-report-with-crewai-and-openai-d6771dea9dad?source=collection_archive---------2-----------------------#2024-11-22](https://towardsdatascience.com/ai-for-bi-building-a-business-information-report-with-crewai-and-openai-d6771dea9dad?source=collection_archive---------2-----------------------#2024-11-22)

## 如何直接从CSV数据创建一个简单的BI报告

[](https://medium.com/@alan-jones?source=post_page---byline--d6771dea9dad--------------------------------)[![Alan Jones](../Images/359379fab1d6685ff08080b98173e67c.png)](https://medium.com/@alan-jones?source=post_page---byline--d6771dea9dad--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d6771dea9dad--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d6771dea9dad--------------------------------) [Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--d6771dea9dad--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d6771dea9dad--------------------------------) ·13分钟阅读·2024年11月22日

--

![](../Images/77ad967ccae64bd31040a3a83abe4fc8.png)

*图片由作者使用ChatGPT绘制*

商业信息应用程序帮助企业利用数据资源做出关键决策，我们将通过AI构建一个这样的应用程序。

AI必将越来越多地在BI工具中发挥作用；更具体来说，基于LLM的应用程序将允许BI应用程序创建可视化图表，通过数据分析提供洞察，并自动生成商业报告。

在本文中，我们将探讨如何通过一个LLM应用程序来帮助创建商业信息。这不会是一个完整的BI应用程序；不过，它将会直接从数据中自动生成图表和文本报告。

我们将通过CrewAI使用OpenAI的API构建一个程序，展示AI在该领域的潜力，并最终实现一个简单的AI驱动的BI应用程序。

我需要指出的是，我使用这些特定的组件是因为它们很方便——我在最近的[教程](https://datavizandai.github.io/2024/09/28/AI_Agents_vs._AI_Pipelines-3A_a_Practical_Guide_to_Coding_Your_LLM_Application.html)中使用了CrewAI（如果你对CrewAI不熟悉，我鼓励你阅读它），并且正在逐渐熟悉它。CrewAI默认使用OpenAI，因此我也选择了它。

另一个LLM，比如Anthropic的Claude、谷歌的Gemini等，也会同样有效，且……
