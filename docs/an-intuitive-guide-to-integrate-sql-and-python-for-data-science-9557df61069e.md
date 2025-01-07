# 直观指南：将 SQL 与 Python 融合以进行数据科学

> 原文：[`towardsdatascience.com/an-intuitive-guide-to-integrate-sql-and-python-for-data-science-9557df61069e?source=collection_archive---------2-----------------------#2024-09-21`](https://towardsdatascience.com/an-intuitive-guide-to-integrate-sql-and-python-for-data-science-9557df61069e?source=collection_archive---------2-----------------------#2024-09-21)

## 学习掌握 MySQL 连接器，这是一个使 Python 能够与 MySQL 数据库交互的库

[](https://eugenia-anello.medium.com/?source=post_page---byline--9557df61069e--------------------------------)![Eugenia Anello](https://eugenia-anello.medium.com/?source=post_page---byline--9557df61069e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9557df61069e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9557df61069e--------------------------------) [Eugenia Anello](https://eugenia-anello.medium.com/?source=post_page---byline--9557df61069e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9557df61069e--------------------------------) ·10 分钟阅读·2024 年 9 月 21 日

--

![](img/3b1ccd0b23662963204dfa0c27397690.png)

照片来源：[ThisisEngineering](https://unsplash.com/it/@thisisengineering) 来自 [Unsplash](https://unsplash.com/it/foto/donna-in-camicia-verde-che-si-siede-davanti-al-computer-64YrPKiguAE)

在我最近的工作经历中，我发现了两个重要的数据管理和分析工具——Python 和 SQL 之间的显著协同效应。

如果你已经沉浸在数据科学的世界中，你可能已经意识到 Python 对于任何数据科学家都是不可或缺的，因为它拥有广泛的库生态系统，支持诸如数据操作、数据可视化和建模等任务。

虽然 Python 在处理复杂数据流程时能够用少量代码实现出色的效果，但 SQL 在高效管理结构化数据、执行查询以及进行读取和修改数据操作方面依然无可匹敌。

在本文中，我将展示一个实际的使用案例，突出展示将 Python 和 SQL 融合以处理 MySQL 数据的好处。让我们开始吧！

**目录：**

+   **什么是关系型数据库？**

+   **设置 MySQL**

+   **开始使用 MySQL Workbench**

+   **将 Python 连接到 MySQL 数据库**

+   **使用 Python 执行常见 SQL 操作**
