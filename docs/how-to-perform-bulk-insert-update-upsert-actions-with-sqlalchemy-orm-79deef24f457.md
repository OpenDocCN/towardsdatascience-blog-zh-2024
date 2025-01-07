# 如何使用 SQLAlchemy ORM 执行批量插入/更新/更新或插入操作

> 原文：[https://towardsdatascience.com/how-to-perform-bulk-insert-update-upsert-actions-with-sqlalchemy-orm-79deef24f457?source=collection_archive---------12-----------------------#2024-05-14](https://towardsdatascience.com/how-to-perform-bulk-insert-update-upsert-actions-with-sqlalchemy-orm-79deef24f457?source=collection_archive---------12-----------------------#2024-05-14)

## 学习使用 SQLAlchemy 最新版本高效地执行批量操作

[](https://lynn-kwong.medium.com/?source=post_page---byline--79deef24f457--------------------------------)[![Lynn G. Kwong](../Images/253fd9755da04beb974f234898f8d2dd.png)](https://lynn-kwong.medium.com/?source=post_page---byline--79deef24f457--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--79deef24f457--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--79deef24f457--------------------------------) [Lynn G. Kwong](https://lynn-kwong.medium.com/?source=post_page---byline--79deef24f457--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--79deef24f457--------------------------------) ·10分钟阅读·2024年5月14日

--

在实践中，我们经常需要同时处理大量的数据记录。当这种情况发生时，性能是一个重要问题。如果处理不当，它将成为应用程序的瓶颈，降低效率和可用性。在这篇文章中，我们将介绍如何使用 SQLAlchemy ORM 执行大规模的插入、更新和更新或插入操作。正如你将看到的，使用最新版本的 SQLAlchemy 2.0，执行批量操作比以前的版本容易得多。然而，在执行这些批量操作时，也有一些需要注意的事项。

![](../Images/82df6d163dc6824b6c69324dda0ab627.png)

图片来自 [PublicDomainPictures](https://pixabay.com/photos/freighter-cargo-ship-industry-port-315201/) 于 Pixabay

## 准备工作

首先，使用 Docker 在本地启动一个 MySQL 服务器：

```py
# Create a volume to persist the data.
$ docker volume create mysql8-data

# Create the container for MySQL.
$ docker run --name mysql8 -d -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=data -p 13306:3306 -v mysql8-data:/var/lib/mysql mysql:8

# Connect to the local MySQL server in Docker.
$ docker exec -it mysql8 mysql -u root -proot

mysql> SELECT VERSION();
+-----------+
| VERSION() |
+-----------+
| 8.4.0   |
+-----------+
1 row in set…
```
