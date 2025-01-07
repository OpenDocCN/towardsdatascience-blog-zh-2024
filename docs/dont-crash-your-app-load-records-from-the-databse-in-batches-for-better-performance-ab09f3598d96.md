# 别让你的应用崩溃：以批次的形式从数据库加载记录以提升性能

> 原文：[https://towardsdatascience.com/dont-crash-your-app-load-records-from-the-databse-in-batches-for-better-performance-ab09f3598d96?source=collection_archive---------6-----------------------#2024-04-18](https://towardsdatascience.com/dont-crash-your-app-load-records-from-the-databse-in-batches-for-better-performance-ab09f3598d96?source=collection_archive---------6-----------------------#2024-04-18)

## 通过高效加载查询来提升你的 Python 应用性能

[](https://mikehuls.medium.com/?source=post_page---byline--ab09f3598d96--------------------------------)[![Mike Huls](../Images/8f9f55a0d25db00799c5d37383b7f5b6.png)](https://mikehuls.medium.com/?source=post_page---byline--ab09f3598d96--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ab09f3598d96--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ab09f3598d96--------------------------------) [Mike Huls](https://mikehuls.medium.com/?source=post_page---byline--ab09f3598d96--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ab09f3598d96--------------------------------) ·7 分钟阅读·2024年4月18日

--

![](../Images/5f4bed285bcfbe44453241b68a82e675.png)

Python 传输小批量数据（由 ChatGPT 生成的图像）

本文讨论了优化 Python 应用与数据库之间通信的技巧，以确保应用流畅运行，同时避免数据库服务器的过载。本文关注一个常见的**低效问题：一次性加载所有查询结果。**

当面对返回大量记录的查询时，**一次性加载所有返回的记录往往既不实际也不可能。** 我们不再将*所有*结果加载到内存中并逐行处理，而是通过本文的方法来**分批加载多个小块数据**。我们不再一次性加载 100 万条记录并处理，而是分批加载 400 次，每次加载 2500 条记录！这样，应用就不必将所有结果加载到内存中，带来了明显的好处：

+   增强的内存使用

+   更好的响应时间感知

+   减少数据库压力

我们还将深入了解这一技术的原理，展示它在幕后是如何工作的。让我们开始编码吧！

# 为什么使用 fetchmany
