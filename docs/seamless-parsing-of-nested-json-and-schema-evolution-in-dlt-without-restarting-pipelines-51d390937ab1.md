# 无需重启管道，Delta Live Tables 中嵌套 JSON 和架构演化的无缝解析

> 原文：[https://towardsdatascience.com/seamless-parsing-of-nested-json-and-schema-evolution-in-dlt-without-restarting-pipelines-51d390937ab1?source=collection_archive---------5-----------------------#2024-10-05](https://towardsdatascience.com/seamless-parsing-of-nested-json-and-schema-evolution-in-dlt-without-restarting-pipelines-51d390937ab1?source=collection_archive---------5-----------------------#2024-10-05)

## 基于一个客户案例研究，本教程介绍了如何使用 Delta Live Tables 处理 JSON 架构演化，而无需重启。

[](https://medium.com/@elahi_irfan?source=post_page---byline--51d390937ab1--------------------------------)[![Irfan Elahi](../Images/f31feb75a9fc8f58d82de12c3d372b83.png)](https://medium.com/@elahi_irfan?source=post_page---byline--51d390937ab1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--51d390937ab1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--51d390937ab1--------------------------------) [Irfan Elahi](https://medium.com/@elahi_irfan?source=post_page---byline--51d390937ab1--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--51d390937ab1--------------------------------) ·阅读时间：15 分钟·2024年10月5日

--

![](../Images/1b6f46531a4eddd4e3d01f650d342ab6.png)

由 DALL-E 生成

***免责声明：*** *我是 Databricks 的解决方案架构师。本文表达的观点和看法仅代表我个人，不一定反映 Databricks 的立场。*

架构演化是数据工程领域中的常见现象。在从数据源提取数据并加载到目标位置时，源架构的变化是不可避免的。当涉及到包含 JSON 负载的源系统时，这一挑战更加突出，比如 PostgreSQL 中的 JSON 类型列。这些 JSON 负载中发生架构变化的可能性很高——新字段可以随时添加，并且通常嵌套在不同的层次中。这些频繁的变化大大增加了构建强大数据管道的复杂性，这些管道需要解析这些架构变化并无缝地演化架构。

由 Delta Lake 格式提供支持的 Databricks 智能平台，提供强大的架构演化支持，在处理数据结构变化时，确保灵活性和弹性。Delta Lake 可以…
