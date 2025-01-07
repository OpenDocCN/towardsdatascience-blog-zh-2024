# 监控 Amazon Kinesis 数据流

> 原文：[`towardsdatascience.com/monitoring-amazon-kinesis-data-streams-825ae21dfa18?source=collection_archive---------11-----------------------#2024-09-04`](https://towardsdatascience.com/monitoring-amazon-kinesis-data-streams-825ae21dfa18?source=collection_archive---------11-----------------------#2024-09-04)

## 探索可观察性功能并提出改进建议

[](https://trrhodes.medium.com/?source=post_page---byline--825ae21dfa18--------------------------------)![Ross Rhodes](https://trrhodes.medium.com/?source=post_page---byline--825ae21dfa18--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--825ae21dfa18--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--825ae21dfa18--------------------------------) [Ross Rhodes](https://trrhodes.medium.com/?source=post_page---byline--825ae21dfa18--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--825ae21dfa18--------------------------------) ·阅读时长 8 分钟·2024 年 9 月 4 日

--

[于 2013 年 11 月发布](https://aws.amazon.com/about-aws/whats-new/2013/11/14/introducing-amazon-kinesis/)，Kinesis 是一个由亚马逊网络服务（AWS）提供的托管解决方案，用于实时处理和分析流数据。Kinesis 提供了几种不同的流媒体服务：

+   *数据流* 用于处理数据记录，无需管理服务器。

+   *视频流* 用于捕获和存储媒体流，例如用于回放。

目前已有大量在线文档深入探讨 Kinesis 的工作原理。[亚马逊官方文档](https://aws.amazon.com/kinesis/) 是一个很好的参考起点。在这篇博客文章中，我们将重点介绍 Kinesis 数据流监控，格式与 [我之前关于 EventBridge 规则的文章](https://medium.com/towards-data-science/monitoring-amazon-eventbridge-rules-127434c58984)类似。

![](img/365b841585229829870ac8ac6c5fac06.png)

图片来源：[Rob Laughter via Unsplash](https://unsplash.com/photos/closeup-photo-of-telescope-facing-water-b4l3Mn4pRKk)。

我们将从 Kinesis 指标的工作原理概述开始，然后介绍当前的、特别有意义的数据流指标。在适用的情况下，我们将指出它们的局限性。接着，我们将给出可以为数据流可观察性增值的建议步骤。

# 数据流指标如何工作

与其他 AWS 服务一样，Kinesis 提供 [CloudWatch 指标](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html) 来监控数据流的性能。提供两个级别的指标：*流* 级别和 *分片* 级别。对于……
