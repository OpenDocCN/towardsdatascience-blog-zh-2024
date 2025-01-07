# 医疗数据分析中的特征工程技术 — 第二部分。

> 原文：[https://towardsdatascience.com/techniques-in-feature-engineering-fc05fd486bc8?source=collection_archive---------4-----------------------#2024-11-20](https://towardsdatascience.com/techniques-in-feature-engineering-fc05fd486bc8?source=collection_archive---------4-----------------------#2024-11-20)

## 医疗数据分析中的特征工程技术，重点讨论现实世界的挑战和实际解决方案。

[](https://medium.com/@panData?source=post_page---byline--fc05fd486bc8--------------------------------)[![Leo Anello 💡](../Images/635ecdec15cda7864d92bf0f1496b6fa.png)](https://medium.com/@panData?source=post_page---byline--fc05fd486bc8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fc05fd486bc8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fc05fd486bc8--------------------------------) [Leo Anello 💡](https://medium.com/@panData?source=post_page---byline--fc05fd486bc8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fc05fd486bc8--------------------------------) ·阅读时长25分钟·2024年11月20日

--

![](../Images/ea3d2f0a6152b49f91defcd5ccddb5e1.png)

由[Irwan](https://unsplash.com/@blogcious?utm_source=medium&utm_medium=referral)拍摄，图片来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# **概述**

在本教程中，我们将继续项目[***特征工程技术：医疗数据分析的现实世界挑战 — 第一部分***](https://medium.com/towards-data-science/feature-engineering-techniques-for-healthcare-data-analysis-part-i-7dfeec78f2a2)，深入探讨一系列新的特征工程技术。项目链接：[**GitHub**](https://github.com/Anello92/feature-engineering-techniques-python)

这一次，我们将利用**领域知识**使特征工程更具效果。这意味着什么呢？它涉及理解我们分析的特定领域，以**从数据集中提取隐藏的见解**。

可见信息通常很直观——比如缺失值、异常值、创建新变量或重新分类现有变量。但揭示隐藏的信息则需要更深入的方法。

这种分析层次通常只有在积累了一定经验并开始处理高级项目时才能实现。我们在这里的重点是**应用基于我们领域特定知识的特征工程**——在这个案例中，是医疗健康领域。
