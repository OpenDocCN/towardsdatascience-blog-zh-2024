# 掌握 Airflow 变量

> 原文：[https://towardsdatascience.com/mastering-airflow-variables-32548a53b3c5?source=collection_archive---------6-----------------------#2024-01-27](https://towardsdatascience.com/mastering-airflow-variables-32548a53b3c5?source=collection_archive---------6-----------------------#2024-01-27)

## 你从 Airflow 检索变量的方式会影响你的 DAG 性能

[](https://gmyrianthous.medium.com/?source=post_page---byline--32548a53b3c5--------------------------------)[![Giorgos Myrianthous](../Images/ff4b116e4fb9a095ce45eb064fde5af3.png)](https://gmyrianthous.medium.com/?source=post_page---byline--32548a53b3c5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--32548a53b3c5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--32548a53b3c5--------------------------------) [Giorgos Myrianthous](https://gmyrianthous.medium.com/?source=post_page---byline--32548a53b3c5--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--32548a53b3c5--------------------------------) ·10分钟阅读·2024年1月27日

--

![](../Images/3e444659f19295a7eafa5424427bb6e4.png)

图片来源：[Daniele Franchi](https://unsplash.com/@daniele_franchi?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在 [Unsplash](https://unsplash.com/photos/white-and-blue-square-illustration-dt0yhDQmJ4k?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

如果多个数据管道需要与同一个 API 端点交互，会发生什么？你真的需要在每个管道中声明这个端点吗？如果这个端点在不久的将来发生变化，你将不得不在每一个文件中更新其值。

Airflow 变量是简单却有价值的构造，用于防止在多个 DAG 中进行冗余声明。它们只是由一个键和一个可序列化为 JSON 的值组成的对象，存储在 Airflow 的元数据数据库中。

那如果你的代码使用了令牌或其他类型的秘密信息呢？将它们硬编码为明文似乎并不是一个安全的做法。除了减少重复外，Airflow 变量还有助于管理敏感信息。在 Airflow 中有六种不同的定义变量的方法，选择合适的方式对于确保安全性和可移植性至关重要。

一个常被忽视的方面是变量检索对 Airflow 性能的影响。每次调度器解析 DAG 文件时（默认间隔为三十秒），它可能会给元数据数据库带来请求压力。

很容易陷入这个陷阱，除非你了解调度器是如何解析 DAG 的，以及……
