# 监控 Amazon EventBridge 规则

> 原文：[`towardsdatascience.com/monitoring-amazon-eventbridge-rules-127434c58984?source=collection_archive---------11-----------------------#2024-08-30`](https://towardsdatascience.com/monitoring-amazon-eventbridge-rules-127434c58984?source=collection_archive---------11-----------------------#2024-08-30)

## 探索指标产品并提出改进建议

[](https://trrhodes.medium.com/?source=post_page---byline--127434c58984--------------------------------)![Ross Rhodes](https://trrhodes.medium.com/?source=post_page---byline--127434c58984--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--127434c58984--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--127434c58984--------------------------------) [Ross Rhodes](https://trrhodes.medium.com/?source=post_page---byline--127434c58984--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--127434c58984--------------------------------) ·6 分钟阅读·2024 年 8 月 30 日

--

来源于[CloudWatch Events](https://docs.aws.amazon.com/whitepapers/latest/introduction-devops-aws/cloudwatch-events.html)，Amazon Web Services (AWS)[于 2019 年 7 月推出 EventBridge](https://aws.amazon.com/about-aws/whats-new/2019/07/introducing-amazon-eventbridge/): 一款无服务器产品，适用于事件驱动架构，现在包括多个组件：

+   *Buses*（代理）和*Rules*，用于将消费者与事件生产者集成。

+   *Scheduling* 用于完全托管的一次性或定期任务调用。

+   *Pipes* 提供生产者与消费者之间的托管集成。

网上有大量资料解释 EventBridge 的工作原理。[产品文档](https://docs.aws.amazon.com/eventbridge/)是一个宝贵的起点。在这篇文章中，我们将深入探讨 EventBridge 规则的监控。

![](img/586963f72400ba0146638ae9e793e24d.png)

图片来源：[berko via Unsplash](https://unsplash.com/@berko)。

我们将从指标工作原理概述开始，然后探讨当前的规则指标产品并识别其局限性。在涵盖了广泛的规则指标后，我们将提出改进建议，以便为客户提供额外的价值。

# 规则指标的工作原理

与其他 AWS 服务一致，EventBridge 规则提供[CloudWatch 指标](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/working_with_metrics.html)来观察性能。一些指标可能会通过一个或多个*维度*来细分，以便查看行为…
