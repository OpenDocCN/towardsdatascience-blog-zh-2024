# 构建持久的数据管道

> 原文：[`towardsdatascience.com/building-durable-data-pipelines-cf3cbf68a7e6?source=collection_archive---------3-----------------------#2024-03-03`](https://towardsdatascience.com/building-durable-data-pipelines-cf3cbf68a7e6?source=collection_archive---------3-----------------------#2024-03-03)

## 构建健壮且可持续的 ETL 数据工程技术

[](https://mshakhomirov.medium.com/?source=post_page---byline--cf3cbf68a7e6--------------------------------)![💡Mike Shakhomirov](https://mshakhomirov.medium.com/?source=post_page---byline--cf3cbf68a7e6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cf3cbf68a7e6--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cf3cbf68a7e6--------------------------------) [💡Mike Shakhomirov](https://mshakhomirov.medium.com/?source=post_page---byline--cf3cbf68a7e6--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cf3cbf68a7e6--------------------------------) ·12 分钟阅读·2024 年 3 月 3 日

--

![](img/9477e71d20a799323560cfb603375e37.png)

使用 [Kandinsky](https://github.com/ai-forever/Kandinsky-2) 生成的 AI 图像

数据管道设计中的**数据持久性**是数据工程领域一个广为人知的痛点。众所周知，数据可用性和数据质量问题会显著增加非增值任务的时间。在本文中，我将讨论确保数据始终可用的数据管道设计模式。我们将讨论一些可能帮助我们构建可持续数据转化过程的技术，这些过程能够确保数据准时交付，并且我们的数据管道可以被描述为健壮、持久，甚至可能是自我修复的。

如果数据管道失败，员工很可能需要执行一系列手动任务，包括不必要的数据来源获取、汇总和处理，以达到预期的结果。

**数据持久性**是数据工程中的一个著名风险因素。依我看，它是目前网上讨论最少的话题之一。然而，仅仅因为你看不见这个问题，并不意味着它不存在。数据工程师可能不会经常谈论它，但这个问题确实存在，它在数据从业者中播下了恐惧的种子，使得数据管道设计成为了一项真正的挑战。

数据可用性和数据质量问题可能导致数据处理的进一步延迟…
