# 构建数据仓库

> 原文：[`towardsdatascience.com/building-a-data-warehouse-9696b238b2da?source=collection_archive---------5-----------------------#2024-02-24`](https://towardsdatascience.com/building-a-data-warehouse-9696b238b2da?source=collection_archive---------5-----------------------#2024-02-24)

## 面向初学者的最佳实践和高级技术

[](https://mshakhomirov.medium.com/?source=post_page---byline--9696b238b2da--------------------------------)![💡Mike Shakhomirov](https://mshakhomirov.medium.com/?source=post_page---byline--9696b238b2da--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9696b238b2da--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9696b238b2da--------------------------------) [💡Mike Shakhomirov](https://mshakhomirov.medium.com/?source=post_page---byline--9696b238b2da--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9696b238b2da--------------------------------) ·12 分钟阅读·2024 年 2 月 24 日

--

![](img/ba3600bd8e30105411ef91dabe1823a2.png)

AI 生成的图像，使用[Kandinsky](https://github.com/ai-forever/Kandinsky-2)

在这个故事中，我想谈谈数据仓库设计以及我们如何组织这个过程。数据建模是数据工程中的一个重要部分。它定义了数据库结构、我们使用的模式以及用于分析的数据物化策略。设计得当时，它有助于确保我们的数据仓库高效运行，满足所有业务需求和成本优化目标。我们将通过使用 dbt 工具作为示例，讨论一些数据仓库设计中的知名最佳实践。我们还将更深入地探讨如何组织构建过程，测试我们的数据集，并使用宏的高级技术来更好地集成工作流和部署。

## 结构

假设我们有一个数据仓库，并且需要处理仓库中的大量 SQL 数据。

> 在我的案例中，使用的是 Snowflake。它是一个很棒的工具，也是当前市场上最流行的解决方案之一，绝对位列前三名。

那么，我们如何构建我们的数据仓库项目结构呢？请看下面这个初学者项目文件夹结构。这是我们运行`dbt init`命令后得到的结构。

```py
.
├── README.md
├── analyses
├── dbt_project.yml
├── logs
│   └── dbt.log
├── macros
├──…
```
