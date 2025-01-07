# 探索递归艺术：使用 Context Free 绘制分形图案

> 原文：[https://towardsdatascience.com/exploring-recursive-art-fractals-with-context-free-58a9f045d0f3?source=collection_archive---------8-----------------------#2024-11-04](https://towardsdatascience.com/exploring-recursive-art-fractals-with-context-free-58a9f045d0f3?source=collection_archive---------8-----------------------#2024-11-04)

## 通过简单的规则和形状生成复杂的图像

[](https://medium.com/@technologger?source=post_page---byline--58a9f045d0f3--------------------------------)[![Diego Penilla](../Images/aa69a740a3b915e5548219fe762dd2ae.png)](https://medium.com/@technologger?source=post_page---byline--58a9f045d0f3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--58a9f045d0f3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--58a9f045d0f3--------------------------------) [Diego Penilla](https://medium.com/@technologger?source=post_page---byline--58a9f045d0f3--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--58a9f045d0f3--------------------------------) ·阅读时间 7 分钟·2024年11月4日

--

随着 AI 生成艺术的崛起，我们很容易忽视那些简单的、基于规则的生成艺术的魅力。本文介绍了**Context Free 艺术**，这是一款可以使用基本规则和递归来创建复杂、美丽设计的工具。

Context Free 艺术非常适合生成分形、树木和其他图案，且几乎不需要编码。

![](../Images/c96a87e2ceb3115da9a8fd960939fa3a.png)

*这件生成艺术作品由作者使用 Context Free 创建。*

这是一款递归、基于规则的生成程序，提供了一种直观的方式来观察复杂的图案是如何从简单、结构化的规则中产生的。通过定义基本的“语法”——形状和变换，我们可以看到复杂的图案一层一层地展开。这种方法不仅提供了一种视觉上令人愉悦的递归探索方式，而且还展示了简单、可重复的规则如何模拟自然系统和抽象数据结构中复杂性的形成。

🔍 让我们探索一下简单的算法是如何产生复杂结构的。如果你觉得有趣，欢迎 [下载软件](https://www.contextfreeart.org)来一起尝试 :)

# 简介

Context Free（以及命令行工具 *cfdg*）是一款数字艺术程序，它通过描述图像生成位图、矢量图，甚至电影。
