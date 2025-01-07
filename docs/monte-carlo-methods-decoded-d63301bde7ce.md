# 蒙特卡洛方法解析

> 原文：[`towardsdatascience.com/monte-carlo-methods-decoded-d63301bde7ce?source=collection_archive---------2-----------------------#2024-02-16`](https://towardsdatascience.com/monte-carlo-methods-decoded-d63301bde7ce?source=collection_archive---------2-----------------------#2024-02-16)

## 用模拟解决复杂问题

[](https://hennie-de-harder.medium.com/?source=post_page---byline--d63301bde7ce--------------------------------)![Hennie de Harder](https://hennie-de-harder.medium.com/?source=post_page---byline--d63301bde7ce--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d63301bde7ce--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d63301bde7ce--------------------------------) [Hennie de Harder](https://hennie-de-harder.medium.com/?source=post_page---byline--d63301bde7ce--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d63301bde7ce--------------------------------) ·阅读时间 16 分钟·2024 年 2 月 16 日

--

**“蒙特卡洛”这个名字来源于位于摩纳哥的著名蒙特卡洛赌场。这个名称的选择并不是因为与这些方法背后的数学原理有直接关联，而是因为它在隐喻上与随机性和机会相联系，这两个元素在赌博和蒙特卡洛模拟中都占据着核心地位。在这篇文章中，我们将讨论这一技术，并展示与项目管理、不规则区域近似以及游戏相关的代码示例。**

现实世界中的系统和过程通常涉及不确定的参数和变量。使用蒙特卡洛方法，你可以明确地对这些不确定性进行建模。企业可以通过理解不同风险的概率和影响，做出更明智的决策。除了决策支持外，你还可以将其用于增强预测模型和/或沟通。

![](img/1f4575cff4c6012c73bcbb1b857ffd2b.png)

装有玻璃球的瓶子。图像由作者使用 Dall·E 创建。

# 基础知识

想象你有一个装满各种颜色玻璃球的大瓶子。问题是，你看不见瓶子里有多少种颜色的球。你想知道如果你不看瓶子，伸手进去，最有可能拿到哪种颜色的球。
