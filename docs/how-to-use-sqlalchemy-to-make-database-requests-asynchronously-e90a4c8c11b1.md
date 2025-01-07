# 如何使用 SQLAlchemy 异步进行数据库请求

> 原文：[`towardsdatascience.com/how-to-use-sqlalchemy-to-make-database-requests-asynchronously-e90a4c8c11b1?source=collection_archive---------5-----------------------#2024-03-05`](https://towardsdatascience.com/how-to-use-sqlalchemy-to-make-database-requests-asynchronously-e90a4c8c11b1?source=collection_archive---------5-----------------------#2024-03-05)

## 学习如何在不同场景下异步使用 SQLAlchemy

[](https://lynn-kwong.medium.com/?source=post_page---byline--e90a4c8c11b1--------------------------------)![Lynn G. Kwong](https://lynn-kwong.medium.com/?source=post_page---byline--e90a4c8c11b1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e90a4c8c11b1--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e90a4c8c11b1--------------------------------) [Lynn G. Kwong](https://lynn-kwong.medium.com/?source=post_page---byline--e90a4c8c11b1--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e90a4c8c11b1--------------------------------) ·阅读时间 8 分钟 ·2024 年 3 月 5 日

--

![](img/97cf8a290b960f29d08d66f3cb914f28.png)

图片来自 WilliamsCreativity (Server Data) 来自 Pixabay

数据库请求是一个典型的 IO 密集型任务，因为它大部分时间都在等待数据库服务器的响应。因此，如果您的应用程序进行大量的数据库请求，那么通过并发执行这些请求，可以显著提高性能，而这正是 SQLAlchemy 支持的，SQLAlchemy 是一个多功能的 Python SQL 工具包和对象关系映射器（ORM）。

此外，异步编程在 Python 中变得越来越流行，尤其是在使用 FastAPI 进行 web 开发时，我们经常需要在协程中进行数据库请求，即在使用 `async def` 语句定义的函数中。不幸的是，我们不能使用经典的同步版本的 SQLAlchemy，而需要创建异步版本的引擎、连接和会话。

在这篇文章中，我们将介绍如何在不同场景下异步使用 SQLAlchemy，即使用纯 SQL 查询、Core 和 ORM。重要的是，我们还将介绍如何在多个异步任务中并发使用它，如果使用得当，这可以显著提高 IO 密集型应用程序的效率。

# 准备工作
