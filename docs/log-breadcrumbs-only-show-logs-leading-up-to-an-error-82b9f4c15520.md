# 面包屑日志：仅显示导致错误的日志

> 原文：[`towardsdatascience.com/log-breadcrumbs-only-show-logs-leading-up-to-an-error-82b9f4c15520?source=collection_archive---------11-----------------------#2024-11-05`](https://towardsdatascience.com/log-breadcrumbs-only-show-logs-leading-up-to-an-error-82b9f4c15520?source=collection_archive---------11-----------------------#2024-11-05)

## 如何使用 Python 的内建日志包记录面包屑日志

[](https://mikehuls.medium.com/?source=post_page---byline--82b9f4c15520--------------------------------)![Mike Huls](https://mikehuls.medium.com/?source=post_page---byline--82b9f4c15520--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--82b9f4c15520--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--82b9f4c15520--------------------------------) [Mike Huls](https://mikehuls.medium.com/?source=post_page---byline--82b9f4c15520--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--82b9f4c15520--------------------------------) ·阅读时间：5 分钟·2024 年 11 月 5 日

--

![](img/976120f05004b36b65170e57a5fbca44.png)

图片由[Daniel Tseng](https://unsplash.com/@daniel840528)提供 / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit)

在本文中，我们将探讨一种有效记录面包屑日志的方法，仅显示**导致错误的日志**。我们将仅使用 Python 的标准日志库创建一个高效的日志设置，**仅在发生异常时捕获 debug 日志**。这种方法提供了**详细的步骤视图，*展示问题发生之前的过程***，同时**减少**杂乱并**最小化 I/O**。让我们开始编码吧！

## 为什么要记录面包屑日志？

当错误发生时，你希望尽可能多的信息来帮助你定位代码中的问题。在这方面，记录大量信息非常有用。

缺点是所有这些日志需要被处理。然后需要写入文件或通过 HTTP 发送到端点，这可能会影响应用程序或服务器的性能。此外，这可能会使日志杂乱无章，导致在错误发生时更难找到相关信息。

面包屑方法“忽略”例如所有`debug`日志，*除非*发生错误。这使你既可以记录大量的错误详细信息，*并且*保持性能和概览水平。

# 设置面包屑日志
