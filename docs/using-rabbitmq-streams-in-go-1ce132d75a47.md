# 在Go中使用RabbitMQ Streams

> 原文：[https://towardsdatascience.com/using-rabbitmq-streams-in-go-1ce132d75a47?source=collection_archive---------5-----------------------#2024-01-15](https://towardsdatascience.com/using-rabbitmq-streams-in-go-1ce132d75a47?source=collection_archive---------5-----------------------#2024-01-15)

## 在本教程中，学习如何在Go中使用RabbitMQ Streams，既可以使用Core，也可以使用插件。

[](https://programmingpercy.medium.com/?source=post_page---byline--1ce132d75a47--------------------------------)[![Percy Bolmér](../Images/34949a468cbbb5c609807903775afddb.png)](https://programmingpercy.medium.com/?source=post_page---byline--1ce132d75a47--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1ce132d75a47--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1ce132d75a47--------------------------------) [Percy Bolmér](https://programmingpercy.medium.com/?source=post_page---byline--1ce132d75a47--------------------------------)

·发布在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1ce132d75a47--------------------------------) ·21分钟阅读·2024年1月15日

--

![](../Images/aeb857ebda89f9995f767e59f79fb5b5.png)

一张由Dall-E创建的表示RabbitMQ Streams的图片

最近我发布了一篇关于RabbitMQ的文章，介绍如何在Go中使用它来构建事件驱动架构。

> 本文中的所有图片由Percy Bolmér制作。Gopher由Takuya Ueda设计，原版Go Gopher由Renée French制作（CC BY 3.0）

这篇文章介绍了RabbitMQ的基础知识，但这次我们将扩展我们的知识，学习**Streams**。本文假设你已经对RabbitMQ队列有基本了解；如果没有，我强烈推荐阅读我之前的文章。

[](https://programmingpercy.tech/blog/event-driven-architecture-using-rabbitmq/?source=post_page-----1ce132d75a47--------------------------------) [## 学习RabbitMQ以构建事件驱动架构（EDA）

### 一篇面向初学者的教程，介绍RabbitMQ的工作原理以及如何在Go中使用RabbitMQ构建事件驱动架构

programmingpercy.tech](https://programmingpercy.tech/blog/event-driven-architecture-using-rabbitmq/?source=post_page-----1ce132d75a47--------------------------------)

我一直依赖RabbitMQ和Kafka的结合。RabbitMQ用于大多数场景，而在需要**重放**、**时间旅行**或**持久化事件**时，我则使用Kafka。

> 时间旅行是指你可以从系统中的某个时间点重放事件

使用两个代理变得更加复杂，因为现在我需要同时部署Kafka并处理两个代理。
