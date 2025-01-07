# 博弈论，第三部分——你是你最常与之相处的五个人的平均值

> 原文：[`towardsdatascience.com/game-theory-part-3-you-are-the-average-of-the-five-people-you-spend-the-most-time-with-a595ee221e43?source=collection_archive---------8-----------------------#2024-11-13`](https://towardsdatascience.com/game-theory-part-3-you-are-the-average-of-the-five-people-you-spend-the-most-time-with-a595ee221e43?source=collection_archive---------8-----------------------#2024-11-13)

## “以牙还牙”是迭代囚徒困境游戏中最佳策略吗？

[](https://saankhya.medium.com/?source=post_page---byline--a595ee221e43--------------------------------)![Saankhya Mondal](https://saankhya.medium.com/?source=post_page---byline--a595ee221e43--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a595ee221e43--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a595ee221e43--------------------------------) [Saankhya Mondal](https://saankhya.medium.com/?source=post_page---byline--a595ee221e43--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a595ee221e43--------------------------------) ·阅读时间 5 分钟·2024 年 11 月 13 日

--

本文将探讨博弈论如何诠释那句流行的格言：“你是你最常与之相处的五个人的平均值。”通过迭代囚徒困境游戏的例子，我们可以看到个体行为和结果是如何受到周围环境中他人策略（无论是合作还是非合作）的影响。

![](img/2c2bfd6990690eeda5488bf5b5762b84.png)

图像由 GPT-4o 生成

在前两篇关于博弈论的文章中，我讨论了囚徒困境问题和迭代囚徒困境游戏。本文是我博弈论系列的第三部分，因此如果你还没有阅读前两篇文章，建议先查看它们。

第一部分讨论了经典的囚徒困境问题，并强调了博弈论在许多现实场景中的应用。第二部分通过一个例子描述了迭代囚徒困境游戏，讲述了两家竞争食品配送平台的首席执行官 Kratika 和 Ishita 如何尝试不同的策略进行竞争。它还讨论了罗伯特·阿克塞尔罗德（Robert Axelrod）的著名比赛，该比赛揭示了最成功的策略具有一些共同特点：它们是“友善的”（从合作开始），具有宽容心（但不会过于宽容……）
