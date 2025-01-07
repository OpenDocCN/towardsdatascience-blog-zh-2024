# Groq 和 AI 硬件 —— 直观且详尽的解释

> 原文：[`towardsdatascience.com/groq-intuitively-and-exhaustively-explained-01e3fcd727ab?source=collection_archive---------1-----------------------#2024-04-12`](https://towardsdatascience.com/groq-intuitively-and-exhaustively-explained-01e3fcd727ab?source=collection_archive---------1-----------------------#2024-04-12)

## 机器学习 | 加速计算 | 人工智能

## 对运行 AI 所需的主要计算机硬件组件的分析，并介绍一种新的重量级玩家。

[](https://medium.com/@danielwarfield1?source=post_page---byline--01e3fcd727ab--------------------------------)![Daniel Warfield](https://medium.com/@danielwarfield1?source=post_page---byline--01e3fcd727ab--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--01e3fcd727ab--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--01e3fcd727ab--------------------------------) [Daniel Warfield](https://medium.com/@danielwarfield1?source=post_page---byline--01e3fcd727ab--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--01e3fcd727ab--------------------------------) ·阅读时长 31 分钟·2024 年 4 月 12 日

--

![](img/79731e2d224f71ba9cf157e39ead4666.png)

“协调解构” 由 Daniel Warfield 使用 MidJourney 制作。除非另有说明，所有图像均由作者提供。所有生成的图像均由 MidJourney 制作。

本文讨论了 Groq，一种全新的计算机硬件方法，它正在革新 AI 在现实世界问题中的应用方式。

在讨论 Groq 之前，我们将首先剖析 AI 的基本概念，并探索一些用于运行 AI 模型的计算机硬件的关键组件。具体来说是：CPU、GPU 和 TPU。我们将从 1975 年开始，通过 Z80 CPU，逐步了解计算机硬件的关键演变，最终构建出对现代系统的理解。

通过理解一些计算机硬件的基本概念和权衡，我们将利用这些理解来探索 Groq 是什么，它如何革新 AI 计算的方式，以及为什么这如此重要。

自然地，从早期的 CPU 到最前沿的价值十亿的 AI 初创公司，涵盖的内容非常广泛。因此，这是一篇相当长的文章。系好安全带，值得一读。
