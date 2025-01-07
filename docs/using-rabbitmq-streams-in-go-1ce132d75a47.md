# 在 Go 中使用 RabbitMQ Streams

> 原文：[`towardsdatascience.com/using-rabbitmq-streams-in-go-1ce132d75a47?source=collection_archive---------5-----------------------#2024-01-15`](https://towardsdatascience.com/using-rabbitmq-streams-in-go-1ce132d75a47?source=collection_archive---------5-----------------------#2024-01-15)

## 在本教程中，学习如何在 Go 中使用 RabbitMQ Streams，既可以使用 Core，也可以使用插件。

[](https://programmingpercy.medium.com/?source=post_page---byline--1ce132d75a47--------------------------------)![Percy Bolmér](https://programmingpercy.medium.com/?source=post_page---byline--1ce132d75a47--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1ce132d75a47--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1ce132d75a47--------------------------------) [Percy Bolmér](https://programmingpercy.medium.com/?source=post_page---byline--1ce132d75a47--------------------------------)

·发布在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1ce132d75a47--------------------------------) ·21 分钟阅读·2024 年 1 月 15 日

--

![](img/aeb857ebda89f9995f767e59f79fb5b5.png)

一张由 Dall-E 创建的表示 RabbitMQ Streams 的图片

最近我发布了一篇关于 RabbitMQ 的文章，介绍如何在 Go 中使用它来构建事件驱动架构。

> 本文中的所有图片由 Percy Bolmér 制作。Gopher 由 Takuya Ueda 设计，原版 Go Gopher 由 Renée French 制作（CC BY 3.0）

这篇文章介绍了 RabbitMQ 的基础知识，但这次我们将扩展我们的知识，学习**Streams**。本文假设你已经对 RabbitMQ 队列有基本了解；如果没有，我强烈推荐阅读我之前的文章。

[](https://programmingpercy.tech/blog/event-driven-architecture-using-rabbitmq/?source=post_page-----1ce132d75a47--------------------------------) [## 学习 RabbitMQ 以构建事件驱动架构（EDA）

### 一篇面向初学者的教程，介绍 RabbitMQ 的工作原理以及如何在 Go 中使用 RabbitMQ 构建事件驱动架构

programmingpercy.tech](https://programmingpercy.tech/blog/event-driven-architecture-using-rabbitmq/?source=post_page-----1ce132d75a47--------------------------------)

我一直依赖 RabbitMQ 和 Kafka 的结合。RabbitMQ 用于大多数场景，而在需要**重放**、**时间旅行**或**持久化事件**时，我则使用 Kafka。

> 时间旅行是指你可以从系统中的某个时间点重放事件

使用两个代理变得更加复杂，因为现在我需要同时部署 Kafka 并处理两个代理。
