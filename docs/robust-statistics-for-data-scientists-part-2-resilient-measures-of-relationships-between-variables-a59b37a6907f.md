# 稳健统计方法：数据科学家的第二部分——变量之间关系的稳健度量

> 原文：[https://towardsdatascience.com/robust-statistics-for-data-scientists-part-2-resilient-measures-of-relationships-between-variables-a59b37a6907f?source=collection_archive---------7-----------------------#2024-03-09](https://towardsdatascience.com/robust-statistics-for-data-scientists-part-2-resilient-measures-of-relationships-between-variables-a59b37a6907f?source=collection_archive---------7-----------------------#2024-03-09)

## 从基础到高级的异常值丰富数据分析技术。

[](https://medium.com/@le_Tomassini?source=post_page---byline--a59b37a6907f--------------------------------)[![Alessandro Tomassini](../Images/e5bf527fafd933239bff6b87005ba457.png)](https://medium.com/@le_Tomassini?source=post_page---byline--a59b37a6907f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a59b37a6907f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a59b37a6907f--------------------------------) [Alessandro Tomassini](https://medium.com/@le_Tomassini?source=post_page---byline--a59b37a6907f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a59b37a6907f--------------------------------) ·15分钟阅读·2024年3月9日

--

![](../Images/066cfc14d21f73f333f7751fdf56dc66.png)

图片由DALL-E生成

理解变量之间的相互关系对于做出基于数据的决策至关重要。当我们准确评估这些联系时，可以增强我们发现的可信度和合法性，这在学术和实践中都至关重要。

数据科学家经常使用皮尔逊相关和线性回归来探讨和衡量变量之间的关系。这些方法假设数据是正态分布、独立且具有一致的分布（或称同方差性），在满足这些条件时表现良好。然而，现实世界中的数据场景很少是理想的。它们通常受到噪声和异常值的干扰，这可能会扭曲传统统计技术的结果，导致错误的结论。本文是我们关于稳健统计方法系列的第二篇，旨在通过深入探讨稳健的替代方法，帮助我们在数据不规则的情况下仍能获得更可靠的见解。

如果你错过了第一部分：
