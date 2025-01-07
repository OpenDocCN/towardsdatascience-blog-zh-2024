# 理解 GA4 BigQuery 导出架构和结构

> 原文：[https://towardsdatascience.com/understanding-the-ga4-bigquery-export-schema-and-structure-3a068ebee4ea?source=collection_archive---------9-----------------------#2024-06-21](https://towardsdatascience.com/understanding-the-ga4-bigquery-export-schema-and-structure-3a068ebee4ea?source=collection_archive---------9-----------------------#2024-06-21)

## 一项定性研究，探讨了一个最奇怪的数据结构，这个数据结构强迫数百万无辜且毫无察觉的分析师使用。

[](https://jim-barlow.medium.com/?source=post_page---byline--3a068ebee4ea--------------------------------)[![Jim Barlow](../Images/1494580717cb92defb17328e4bae1b13.png)](https://jim-barlow.medium.com/?source=post_page---byline--3a068ebee4ea--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3a068ebee4ea--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3a068ebee4ea--------------------------------) [Jim Barlow](https://jim-barlow.medium.com/?source=post_page---byline--3a068ebee4ea--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3a068ebee4ea--------------------------------) ·阅读时间 11 分钟·2024 年 6 月 21 日

--

![](../Images/16fb35619de336e01dbd7b4b7da9c038.png)

你想要的数据肯定在某个地方，你只需要找出如何解开它……照片由 [Dean Ward](https://unsplash.com/@deanward?utm_source=medium&utm_medium=referral) 提供，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍

目前估计有 [1560 万](https://trends.builtwith.com/analytics/Google-Analytics-4)个网站在使用 Google Analytics 4，这意味着 BigQuery GA4 导出可能是历史上最广泛导出的数据架构之一。Google Analytics 4 数据可以通过网页用户界面或通过 API 直接访问 Looker Studio，但如果你想：

+   **掌控**你的数据，超越 Google 的数据保留政策，

+   **归档**你的数据，以防止可能的数据丢失，

+   **结合**你的数据与其他内部数据源，

+   **增强**你的数据，来自额外的外部数据源、API 或 LLM，或者

+   **构建**自定义自动化工作流

然后，推荐的做法是启用[GA4 导出到 BigQuery](https://support.google.com/analytics/answer/9823238?hl=en)，这是一个非常简单的设置和配置过程。

太棒了！工作完成！

还不完全是。一旦数据开始出现在每日的 BigQuery 导出中，你可能会注意到结构有些不规则，这使得直接处理非常困难。这个……
