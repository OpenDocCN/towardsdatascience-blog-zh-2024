# 使用CrewAI和Streamlit将客户反馈转化为可操作的洞察。

> 原文：[https://towardsdatascience.com/transform-customer-feedback-into-actionable-insights-with-crewai-and-streamlit-06b6f482f5ae?source=collection_archive---------2-----------------------#2024-12-19](https://towardsdatascience.com/transform-customer-feedback-into-actionable-insights-with-crewai-and-streamlit-06b6f482f5ae?source=collection_archive---------2-----------------------#2024-12-19)

## AI用于商业智能

## 构建一个AI驱动的应用程序，分析非结构化反馈，生成有洞察力的报告，并创建交互式可视化。

[](https://medium.com/@alan-jones?source=post_page---byline--06b6f482f5ae--------------------------------)[![Alan Jones](../Images/359379fab1d6685ff08080b98173e67c.png)](https://medium.com/@alan-jones?source=post_page---byline--06b6f482f5ae--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--06b6f482f5ae--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--06b6f482f5ae--------------------------------) [Alan Jones](https://medium.com/@alan-jones?source=post_page---byline--06b6f482f5ae--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--06b6f482f5ae--------------------------------) ·13分钟阅读·2024年12月19日

--

![](../Images/66483df16e439703e57a9542a796466c.png)

*基于图片来自* [*Temel*](https://pixabay.com/users/colibrie-15745216/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=9126721) *来自* [*Pixabay*](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=9126721)

新的AI代理工具使得自动化数据分析变得非常简单。当与像Streamlit这样的可视化平台结合时，创建一个具有视觉冲击力的商业报告应用程序变得轻而易举。

在本教程中，我们将重点分析客户反馈。这种分析以及对客户情感的良好理解对于确保产品符合预期并解决质量问题至关重要。被认为质量差的产品是不会畅销的。

然而，分析客户反馈可能涉及处理大量的非结构化数据——这是一项可能令人生畏的任务。这正是大型语言模型的强项，它能够从这些数据中提取有意义的洞察。

我们的第一步是明确目标。

假设我们有一个来自在线零售商的客户反馈信息列表。我们的目标是生成一份高层报告，突出表现最好的产品，识别表现不佳的产品……
