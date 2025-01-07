# 深入探索：更详细地了解 4 个 Airflow 内部细节

> 原文：[`towardsdatascience.com/airflow-internals-851f4a440028?source=collection_archive---------7-----------------------#2024-02-03`](https://towardsdatascience.com/airflow-internals-851f4a440028?source=collection_archive---------7-----------------------#2024-02-03)

## 你可能错过的四个 Apache Airflow 内部细节

[](https://gmyrianthous.medium.com/?source=post_page---byline--851f4a440028--------------------------------)![Giorgos Myrianthous](https://gmyrianthous.medium.com/?source=post_page---byline--851f4a440028--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--851f4a440028--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--851f4a440028--------------------------------) [Giorgos Myrianthous](https://gmyrianthous.medium.com/?source=post_page---byline--851f4a440028--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--851f4a440028--------------------------------) ·6 分钟阅读·2024 年 2 月 3 日

--

![](img/1269d9de6e00493515bf3f1a54dd25ab.png)

通过 DALL-E 生成的图像

我已经与 Airflow 一起工作了超过三年，总体上我对它非常有信心。它是一个强大的调度器，帮助我快速并且可扩展地构建数据管道，而在我打算实现的大多数功能中，它自带了许多工具。

最近，在准备 Airflow 认证考试时，我遇到了许多我完全不知道的事情。这也是我写这篇文章并与大家分享一些令我大开眼界的 Airflow 内部细节的动力！

## 1\. 调度器只解析包含特定关键词的文件

**Airflow 调度器只会解析包含** `**airflow**` **或** `**dag**` **关键词的文件！** 是的，你没听错！如果 DAG 文件夹下的某个文件没有包含这两个关键词中的至少一个，它将不会被调度器解析。

如果你想修改这个规则，使得调度器不再需要这个要求，你可以简单地将 `DAG_DISCOVERY_SAFE_MODE` 配置设置为 `False`。在这种情况下，调度器将解析你 DAG 文件夹下的所有文件（`/dags`）。
