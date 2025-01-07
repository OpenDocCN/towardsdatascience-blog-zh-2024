# 基于使用量的API计费和计量的实时分析解决方案

> 原文：[https://towardsdatascience.com/real-time-analytics-solution-for-usage-based-api-billing-and-metering-f9e7a350f707?source=collection_archive---------9-----------------------#2024-05-24](https://towardsdatascience.com/real-time-analytics-solution-for-usage-based-api-billing-and-metering-f9e7a350f707?source=collection_archive---------9-----------------------#2024-05-24)

## 设计一个使用Apache APISIX、Redpanda和Apache Pinot进行API调用使用量跟踪的实时分析管道。

[](https://dunith.medium.com/?source=post_page---byline--f9e7a350f707--------------------------------)[![Dunith Danushka](../Images/ad8dc098a31c073956d145fd4a0406bf.png)](https://dunith.medium.com/?source=post_page---byline--f9e7a350f707--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f9e7a350f707--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f9e7a350f707--------------------------------) [Dunith Danushka](https://dunith.medium.com/?source=post_page---byline--f9e7a350f707--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f9e7a350f707--------------------------------) ·11分钟阅读·2024年5月24日

--

![](../Images/f8a4c14e07da6380cbb3d8968aaa3a96.png)

图片由 [Doris Morgan](https://unsplash.com/@d_morgan?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

**免责声明：** *本文的作者是Redpanda的开发者倡导者，Redpanda是本文所讨论解决方案的关键组成部分。作者还在API管理和Apache Pinot方面拥有丰富的经验。因此，所提出的解决方案是这些技术的结合，旨在解决一个普遍存在的问题。*

API业务指的是将其服务或功能打包为一组API（应用程序编程接口）产品的公司。这些API可以销售给新客户和现有客户，客户可以将这些功能集成到他们自己的应用程序中。公司可以通过根据客户使用API的情况收费来生成收入。

运营API业务的公司需要一个数据基础设施组件来跟踪API调用量并相应地对消费者进行计费。

在本文中，我将展示一个使用 [Apache APISIX](https://apisix.apache.org/)、[Redpanda](http://redpanda.com) 和 [Apache Pinot](https://pinot.apache.org/) 构建实时API使用跟踪解决方案的参考架构。本文更侧重于“为什么”，而非“如何”。可以将其视为一个解决方案设计……
