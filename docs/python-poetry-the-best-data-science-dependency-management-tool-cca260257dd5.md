# Python Poetry —— 最佳数据科学依赖管理工具？

> 原文：[https://towardsdatascience.com/python-poetry-the-best-data-science-dependency-management-tool-cca260257dd5?source=collection_archive---------3-----------------------#2024-07-30](https://towardsdatascience.com/python-poetry-the-best-data-science-dependency-management-tool-cca260257dd5?source=collection_archive---------3-----------------------#2024-07-30)

## Poetry 使得部署机器学习应用变得轻而易举 —— 快来了解如何操作！

[](https://medium.com/@radecicdario?source=post_page---byline--cca260257dd5--------------------------------)[![Dario Radečić](../Images/41882a3b30bab9da43d66a59f1df366b.png)](https://medium.com/@radecicdario?source=post_page---byline--cca260257dd5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cca260257dd5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cca260257dd5--------------------------------) [Dario Radečić](https://medium.com/@radecicdario?source=post_page---byline--cca260257dd5--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cca260257dd5--------------------------------) ·8分钟阅读·2024年7月30日

--

![](../Images/fd16f9d0c8e9893fa0e0b3e104f9469d.png)

图片由 [Prachi Gautam](https://unsplash.com/@prachi30gautam?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

如果我每次遇到缺失的 Python 依赖或版本不匹配时都有一美元，那我虽然不能成为百万富翁，但你大概明白我的意思。

依赖管理是数据科学中的一个常见问题，有许多潜在的解决方案。始终建议使用**虚拟环境**，但这仅仅是开始。通常接下来要跟踪已安装的包。**那么它们的依赖呢？** 以及它们的依赖的依赖呢？这是一个递归的噩梦。

[Poetry](https://python-poetry.org) 可能是你正在寻找的解决方案。它旨在成为一个一站式的依赖管理工具，甚至可以用于发布 Python 包。

今天，你将本地构建一个简单的机器学习应用程序，并将其推送到远程计算实例。如果 Poetry 能兑现它的承诺，远程设置应该像运行一个简单的 shell 命令一样简单。

# 如何开始使用 Poetry

使用 Poetry 的一个小小不便是，你不能直接通过 `pip install` 命令开始。它需要额外的命令行工具。

## 安装 Poetry
