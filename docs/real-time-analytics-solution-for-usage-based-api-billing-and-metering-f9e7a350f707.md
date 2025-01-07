# 基于使用量的 API 计费和计量的实时分析解决方案

> 原文：[`towardsdatascience.com/real-time-analytics-solution-for-usage-based-api-billing-and-metering-f9e7a350f707?source=collection_archive---------9-----------------------#2024-05-24`](https://towardsdatascience.com/real-time-analytics-solution-for-usage-based-api-billing-and-metering-f9e7a350f707?source=collection_archive---------9-----------------------#2024-05-24)

## 设计一个使用 Apache APISIX、Redpanda 和 Apache Pinot 进行 API 调用使用量跟踪的实时分析管道。

[](https://dunith.medium.com/?source=post_page---byline--f9e7a350f707--------------------------------)![Dunith Danushka](https://dunith.medium.com/?source=post_page---byline--f9e7a350f707--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f9e7a350f707--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f9e7a350f707--------------------------------) [Dunith Danushka](https://dunith.medium.com/?source=post_page---byline--f9e7a350f707--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f9e7a350f707--------------------------------) ·11 分钟阅读·2024 年 5 月 24 日

--

![](img/f8a4c14e07da6380cbb3d8968aaa3a96.png)

图片由 [Doris Morgan](https://unsplash.com/@d_morgan?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

**免责声明：** *本文的作者是 Redpanda 的开发者倡导者，Redpanda 是本文所讨论解决方案的关键组成部分。作者还在 API 管理和 Apache Pinot 方面拥有丰富的经验。因此，所提出的解决方案是这些技术的结合，旨在解决一个普遍存在的问题。*

API 业务指的是将其服务或功能打包为一组 API（应用程序编程接口）产品的公司。这些 API 可以销售给新客户和现有客户，客户可以将这些功能集成到他们自己的应用程序中。公司可以通过根据客户使用 API 的情况收费来生成收入。

运营 API 业务的公司需要一个数据基础设施组件来跟踪 API 调用量并相应地对消费者进行计费。

在本文中，我将展示一个使用 [Apache APISIX](https://apisix.apache.org/)、[Redpanda](http://redpanda.com) 和 [Apache Pinot](https://pinot.apache.org/) 构建实时 API 使用跟踪解决方案的参考架构。本文更侧重于“为什么”，而非“如何”。可以将其视为一个解决方案设计……
