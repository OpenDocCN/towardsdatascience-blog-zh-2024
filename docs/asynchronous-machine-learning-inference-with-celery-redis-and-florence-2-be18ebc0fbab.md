# 使用 Celery、Redis 和 Florence 2 进行异步机器学习推理

> 原文：[`towardsdatascience.com/asynchronous-machine-learning-inference-with-celery-redis-and-florence-2-be18ebc0fbab?source=collection_archive---------3-----------------------#2024-07-19`](https://towardsdatascience.com/asynchronous-machine-learning-inference-with-celery-redis-and-florence-2-be18ebc0fbab?source=collection_archive---------3-----------------------#2024-07-19)

## 一份简单的教程，帮助你入门异步机器学习推理

[](https://medium.com/@CVxTz?source=post_page---byline--be18ebc0fbab--------------------------------)![Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--be18ebc0fbab--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--be18ebc0fbab--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--be18ebc0fbab--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--be18ebc0fbab--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--be18ebc0fbab--------------------------------) ·阅读时间 6 分钟·2024 年 7 月 19 日

--

![](img/573d6ccd2ee339c929f687cba908cbf9.png)

图片由 [Fabien BELLANGER](https://unsplash.com/@fabbel78?utm_source=medium&utm_medium=referral) 提供，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

大多数机器学习服务教程都专注于实时同步服务，这种方式可以即时响应预测请求。然而，这种方法在面对流量激增时可能会遇到困难，并且不适合长时间运行的任务。它还需要更强大的机器来快速响应，如果客户端或服务器发生故障，预测结果通常会丢失。

在这篇博客中，我们将演示如何使用 Celery 和 Redis 将机器学习模型作为异步工作者运行。我们将使用 Florence 2 基础模型，这是一个以其卓越表现而闻名的视觉语言模型。本教程将提供一个最小但功能完善的示例，您可以根据自己的用例进行调整和扩展。

我们解决方案的核心基于 Celery，这是一个 Python 库，它为我们实现了这种客户端/工作者逻辑。它允许我们将计算任务分配到多个工作者，提高机器学习推理应用在高负载和不可预测负载下的可扩展性。

该过程如下所示：

1.  客户端将任务及一些参数提交到由中介（我们示例中是 Redis）管理的队列中。
