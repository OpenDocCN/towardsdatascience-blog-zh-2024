# 数据工程：增量数据加载策略

> 原文：[https://towardsdatascience.com/data-engineering-incremental-data-loading-strategies-b4d62f9dab28?source=collection_archive---------1-----------------------#2024-03-17](https://towardsdatascience.com/data-engineering-incremental-data-loading-strategies-b4d62f9dab28?source=collection_archive---------1-----------------------#2024-03-17)

## 制定策略和解决方案架构，以实现从各种数据源逐步加载数据。

[](https://husseinjundi.medium.com/?source=post_page---byline--b4d62f9dab28--------------------------------)[![Hussein Jundi](../Images/721d74f2b902cff791715ffad7a8791f.png)](https://husseinjundi.medium.com/?source=post_page---byline--b4d62f9dab28--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b4d62f9dab28--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b4d62f9dab28--------------------------------) [Hussein Jundi](https://husseinjundi.medium.com/?source=post_page---byline--b4d62f9dab28--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b4d62f9dab28--------------------------------) ·阅读时间：10分钟·2024年3月17日

--

大数据时代需要高效且具成本效益的数据处理策略。在处理各类关键数据源时，增量数据摄取成为了首选方案，尤其是在这些数据源以高速度和低延迟生成数据的情况下。

![](../Images/509d5ee99926c4520bd602d0d447c354.png)

图片由[Santshree Sinha](https://unsplash.com/@alphayaatri?utm_source=medium&utm_medium=referral)提供，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

多年来，我作为数据工程师和分析师，致力于将多个数据源集成到企业数据平台中。在尝试逐步摄取并加载数据到目标数据湖和数据库时，我遇到了一个又一个复杂性。当数据像散落在灰尘中、遗留系统角落里的碎片时，复杂性尤为显现。我需要深入这些系统，找到黄金接口、时间戳和标识符，期望能够实现无缝且增量的数据集成。

这是一个常见的场景，当新的数据源需要用于分析用例时，工程师和分析师常常会面临这种情况。顺利实施数据摄取是一项技艺，许多工程师和分析师都致力于完善这一技能。然而，这有时是遥不可及的，尤其是当考虑到源系统及其提供的数据时，事情往往会变得混乱且复杂……
