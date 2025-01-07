# ChatGPT 并不总是能理解 SQL，但这个 Python 工具可以

> 原文：[`towardsdatascience.com/say-goodbye-to-sql-headaches-with-this-python-tool-75099f5ff33d?source=collection_archive---------2-----------------------#2024-01-15`](https://towardsdatascience.com/say-goodbye-to-sql-headaches-with-this-python-tool-75099f5ff33d?source=collection_archive---------2-----------------------#2024-01-15)

![](img/7f533659c48f254a71a1f16cf7d400db.png)

图片来自[2023852](https://pixabay.com/users/2023852-2023852/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1192669)的[Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1192669)

## SQLGlot — 一站式 SQL 解析与操作 Python 库

[](https://christophertao.medium.com/?source=post_page---byline--75099f5ff33d--------------------------------)![Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--75099f5ff33d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--75099f5ff33d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--75099f5ff33d--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--75099f5ff33d--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--75099f5ff33d--------------------------------) ·10 分钟阅读·2024 年 1 月 15 日

--

如果你是一个日常处理数据的开发者或分析师，SQL 查询将是许多不同职位所需的基本技能之一。记得当我在大学当辅导员时，有一个学生抱怨 SQL 是最糟糕的编程语言。嗯，关于 SQL 是否算作一种“编程语言”，我猜这可能会引起争议。不过，毫无疑问，SQL 在语法上是相当结构化和严格的。因此，相对来说，实现一个工具来完全理解它的意义会更容易。并不需要 ChatGPT :)

在这篇文章中，我将介绍一个库，它帮助我们解析 SQL 查询，甚至能够程序化地构建查询。如果你不知道为什么我们需要这样做，那么继续阅读下面的内容对你来说会更重要。

# 1\. 安装

![](img/63bffb8ceb480bdb056a738f4d347dee.png)

图片来自[AndreasAux](https://pixabay.com/users/andreasaux-148268/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=326923)的[Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=326923)

一如既往的无聊部分，不过我还是得有这一部分。要安装这个库，只需运行以下命令。

```py
pip install sqlglot
```
