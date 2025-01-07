# 特征工程技术：

> 原文：[https://towardsdatascience.com/feature-engineering-techniques-for-healthcare-data-analysis-part-i-7dfeec78f2a2?source=collection_archive---------1-----------------------#2024-11-15](https://towardsdatascience.com/feature-engineering-techniques-for-healthcare-data-analysis-part-i-7dfeec78f2a2?source=collection_archive---------1-----------------------#2024-11-15)

## 现实世界中的医疗数据挑战 — 第一部分。

[](https://medium.com/@panData?source=post_page---byline--7dfeec78f2a2--------------------------------)[![Leo Anello 💡](../Images/635ecdec15cda7864d92bf0f1496b6fa.png)](https://medium.com/@panData?source=post_page---byline--7dfeec78f2a2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7dfeec78f2a2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7dfeec78f2a2--------------------------------) [Leo Anello 💡](https://medium.com/@panData?source=post_page---byline--7dfeec78f2a2--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7dfeec78f2a2--------------------------------) ·38分钟阅读·2024年11月15日

--

![](../Images/3e08118ec7d933e86108241155636fbe.png)

图片由 [Piron Guillaume](https://unsplash.com/@gpiron?utm_source=medium&utm_medium=referral) 提供，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在这个项目中，我们将深入探讨**医疗数据的特征工程**，其中精确度至关重要。这个项目是一个全面的过程，将带你经历数据分析的每个阶段。享受这个旅程，并不要错过过程中推荐的资源。

**医院再入院** — 患者出院后不久再次住院 — 是一个高成本问题，暴露了医疗系统中的漏洞。在美国，仅糖尿病患者的再入院每年就花费超过**3亿美元**。

通过识别高风险患者，医疗团队可以进一步调查，在许多情况下，预防这些再入院。这种主动的方法不仅节省了成本，还能提高**护理质量**。

**糖尿病**是**全球第七大死亡原因**，在美国影响**2360万**人，全球范围内有更多人受其影响。**美国糖尿病协会**报告称，治疗糖尿病和前糖尿病患者是全球最高的医疗支出。

全球影响**3.5亿人**，每年因相关并发症，特别是心血管疾病导致**300万人死亡**，显然迫切需要采取主动护理。
