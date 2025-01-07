# 线性规划：库存切割问题

> 原文：[`towardsdatascience.com/linear-programming-the-stock-cutting-problem-dc6ba3bf3de1?source=collection_archive---------4-----------------------#2024-08-22`](https://towardsdatascience.com/linear-programming-the-stock-cutting-problem-dc6ba3bf3de1?source=collection_archive---------4-----------------------#2024-08-22)

## 第二部分 — 线性规划示例深入分析

[](https://medium.com/@jarom.hulet?source=post_page---byline--dc6ba3bf3de1--------------------------------)![Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page---byline--dc6ba3bf3de1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--dc6ba3bf3de1--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dc6ba3bf3de1--------------------------------) [Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page---byline--dc6ba3bf3de1--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dc6ba3bf3de1--------------------------------) ·12 分钟阅读·2024 年 8 月 22 日

--

![](img/130ab2fd49d75f7aab2b9c9026574fbe.png)

图片由 Mark Stebnicki 提供，来自 Pexels.com

这篇文章深入探讨了线性规划如何解决一个特定的问题——‘库存切割’问题。在开始深入讲解本系列中的线性规划细节之前，我想提供一个具体的例子。这篇文章将使用一些优化术语，这些术语没有在文中定义——我写了一篇优化基础的文章，涵盖了这些术语和其他基本概念。

**在本文中，我们将讨论：**

1.  **库存切割问题定义**

**2. 问题难度**

**3. 用 Python 解决问题**

这里是该系列的第一篇文章和我提到的优化基础文章的链接：

[](/linear-programming-optimization-foundations-2f12770f66ca?source=post_page-----dc6ba3bf3de1--------------------------------) ## 线性规划优化：基础知识

### 第一部分 — 基本概念和例子

towardsdatascience.com
