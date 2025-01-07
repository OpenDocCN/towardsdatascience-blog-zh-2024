# 线性规划：库存切割问题

> 原文：[https://towardsdatascience.com/linear-programming-the-stock-cutting-problem-dc6ba3bf3de1?source=collection_archive---------4-----------------------#2024-08-22](https://towardsdatascience.com/linear-programming-the-stock-cutting-problem-dc6ba3bf3de1?source=collection_archive---------4-----------------------#2024-08-22)

## 第二部分 — 线性规划示例深入分析

[](https://medium.com/@jarom.hulet?source=post_page---byline--dc6ba3bf3de1--------------------------------)[![Jarom Hulet](../Images/0fdeb1a2df90cccdd8f2f4b84d5e54eb.png)](https://medium.com/@jarom.hulet?source=post_page---byline--dc6ba3bf3de1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--dc6ba3bf3de1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--dc6ba3bf3de1--------------------------------) [Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page---byline--dc6ba3bf3de1--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dc6ba3bf3de1--------------------------------) ·12分钟阅读·2024年8月22日

--

![](../Images/130ab2fd49d75f7aab2b9c9026574fbe.png)

图片由Mark Stebnicki提供，来自Pexels.com

这篇文章深入探讨了线性规划如何解决一个特定的问题——‘库存切割’问题。在开始深入讲解本系列中的线性规划细节之前，我想提供一个具体的例子。这篇文章将使用一些优化术语，这些术语没有在文中定义——我写了一篇优化基础的文章，涵盖了这些术语和其他基本概念。

**在本文中，我们将讨论：**

1.  **库存切割问题定义**

**2. 问题难度**

**3. 用Python解决问题**

这里是该系列的第一篇文章和我提到的优化基础文章的链接：

[](/linear-programming-optimization-foundations-2f12770f66ca?source=post_page-----dc6ba3bf3de1--------------------------------) [## 线性规划优化：基础知识

### 第一部分 — 基本概念和例子

towardsdatascience.com](/linear-programming-optimization-foundations-2f12770f66ca?source=post_page-----dc6ba3bf3de1--------------------------------)
