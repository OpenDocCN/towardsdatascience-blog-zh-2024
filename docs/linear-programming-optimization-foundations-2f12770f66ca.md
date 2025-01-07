# 线性规划优化：基础

> 原文：[https://towardsdatascience.com/linear-programming-optimization-foundations-2f12770f66ca?source=collection_archive---------3-----------------------#2024-07-22](https://towardsdatascience.com/linear-programming-optimization-foundations-2f12770f66ca?source=collection_archive---------3-----------------------#2024-07-22)

## 第一部分 - 基本概念与示例

[](https://medium.com/@jarom.hulet?source=post_page---byline--2f12770f66ca--------------------------------)[![Jarom Hulet](../Images/0fdeb1a2df90cccdd8f2f4b84d5e54eb.png)](https://medium.com/@jarom.hulet?source=post_page---byline--2f12770f66ca--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2f12770f66ca--------------------------------)[![数据科学前沿](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2f12770f66ca--------------------------------) [Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page---byline--2f12770f66ca--------------------------------)

·发布于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--2f12770f66ca--------------------------------) ·11分钟阅读·2024年7月22日

--

线性规划是一种强大的优化技术，广泛应用于许多领域，以帮助改进决策过程。这是一个系列文章的第一部分，将涵盖与线性规划相关的重要主题。本文是最基础的部分，旨在介绍基本概念，之后将深入探讨更高级的线性规划话题。

在本文中，我们将：

1.  讨论什么构成了一个线性规划问题

1.  了解线性规划是如何工作的以及它为何如此强大

1.  在Python中运行一个线性规划示例

我个人认为，例子是学习技术主题的非常有效的途径。出于这个原因，本文将通过一个简单的例子进行讲解。我将在下一节介绍这个例子。

在我们深入之前，如果你对优化的基本概念和术语不太熟悉，我写了一篇优化入门文章，建议你在继续阅读之前先查看一下（本文中有很多常见的优化术语，我没有在文中定义）。
