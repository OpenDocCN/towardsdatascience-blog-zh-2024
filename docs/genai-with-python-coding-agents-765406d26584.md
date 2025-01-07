# 使用Python的生成式AI：编码智能体

> 原文：[https://towardsdatascience.com/genai-with-python-coding-agents-765406d26584?source=collection_archive---------1-----------------------#2024-09-09](https://towardsdatascience.com/genai-with-python-coding-agents-765406d26584?source=collection_archive---------1-----------------------#2024-09-09)

## 构建一个数据科学家AI，可以使用SQL查询数据库，使用Python分析数据，使用HTML编写报告，并进行机器学习（无需GPU，无需APIKEY）

[](https://maurodp.medium.com/?source=post_page---byline--765406d26584--------------------------------)[![Mauro Di Pietro](../Images/3586d9d3238d904a1e1fa39c77b59d3f.png)](https://maurodp.medium.com/?source=post_page---byline--765406d26584--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--765406d26584--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--765406d26584--------------------------------) [Mauro Di Pietro](https://maurodp.medium.com/?source=post_page---byline--765406d26584--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--765406d26584--------------------------------) ·阅读时间：10分钟·2024年9月9日

--

![](../Images/7ec8e3d47a824f75090169c3dd7566f8.png)

图片来源：[Goran Ivos](https://unsplash.com/@goran_ivos?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

*(除非另有注明，所有图片均来自作者)*

## 简介

在生成式AI中，**智能体**是设计用于处理顺序推理的AI系统，具有在LLM的通用知识库不足时执行外部工具（如数据库查询、网页搜索）的选项。简单来说，一个普通的AI聊天机器人在不知道如何回答问题时会生成随机文本。而智能体则会激活其工具来填补空白并给出具体回应。

更准确地说，AI智能体能够在其环境内进行自主决策和行动，以实现特定目标。它们与LLM的区别在于能够互动并执行实际任务，而不仅仅是处理文本。LLM擅长自然语言理解和生成，但不会在文本回应之外自主执行任务。而AI智能体则能适应、学习，并以更高的独立性运作，使其适用于超越文本处理的动态应用。
