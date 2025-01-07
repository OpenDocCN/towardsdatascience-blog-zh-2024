# OpenAI 嵌入技术与聚类分析在调查分析中的应用 — 操作指南

> 原文：[https://towardsdatascience.com/openai-embeddings-and-clustering-for-survey-analysis-a-how-to-guide-5767b67dac0a?source=collection_archive---------9-----------------------#2024-10-25](https://towardsdatascience.com/openai-embeddings-and-clustering-for-survey-analysis-a-how-to-guide-5767b67dac0a?source=collection_archive---------9-----------------------#2024-10-25)

## 如何从调查数据中获取洞察，并使用嵌入技术和大语言模型提取话题

[](https://medium.com/@alejandra.vlerick?source=post_page---byline--5767b67dac0a--------------------------------)[![Alejandra Vlerick](../Images/02280890ed87239c75cbcbfa7c5d686c.png)](https://medium.com/@alejandra.vlerick?source=post_page---byline--5767b67dac0a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5767b67dac0a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5767b67dac0a--------------------------------) [Alejandra Vlerick](https://medium.com/@alejandra.vlerick?source=post_page---byline--5767b67dac0a--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5767b67dac0a--------------------------------) ·7分钟阅读·2024年10月25日

--

![](../Images/c68739260627ea532b1c1058385a77db.png)

图片来源：[Olav Ahrens Røtne](https://unsplash.com/@olav_ahrens?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

离我换工作已经整整4个月了，这段时间我已经安顿下来并继续我的小型副项目。

最新的一个工具是一个调查分析工具，是我公司的一位产品负责人请求的。他必须每季度查看成千上万的公司范围的调查回应，试图提取出可执行的业务改进措施

现在这个工具正在被使用，并且（希望）为产品负责人和分析师节省了大量时间，我编写了这篇操作指南，帮助你创建类似的工具。

# **项目流程**

我们将调查问卷的回答作为输入数据框（使用 pandas）。关键列是每个用户留下的评论。其他字段如部门、职位和提交日期也可以进行分析，但为了最小可行产品，我决定保持变量简单且少。

本研究的数据是通过在线调查收集的，并且将保持机密。为了本文的目的，实际数据未显示；图片展示了一个较小样本的分析过程……
