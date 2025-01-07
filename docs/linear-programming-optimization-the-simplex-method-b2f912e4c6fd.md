# 线性规划优化：单纯形法

> 原文：[`towardsdatascience.com/linear-programming-optimization-the-simplex-method-b2f912e4c6fd?source=collection_archive---------7-----------------------#2024-09-10`](https://towardsdatascience.com/linear-programming-optimization-the-simplex-method-b2f912e4c6fd?source=collection_archive---------7-----------------------#2024-09-10)

## 第三部分：算法的幕后

[](https://medium.com/@jarom.hulet?source=post_page---byline--b2f912e4c6fd--------------------------------)![Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page---byline--b2f912e4c6fd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b2f912e4c6fd--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b2f912e4c6fd--------------------------------) [Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page---byline--b2f912e4c6fd--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b2f912e4c6fd--------------------------------) ·13 分钟阅读·2024 年 9 月 10 日

--

![](img/9faaa945b04ddf866a45a7b2e4915639.png)

图片来自 Pexels.com 的 Daniel Cassey Pahati

到目前为止，本系列已涵盖了线性规划的基础知识。在这篇文章中，我们将从基本概念转向更深入的算法细节！这篇文章将介绍单纯形法，它是解决线性规划问题时常用的算法。虽然我们将通过手工计算一个简单的线性规划例子来应用单纯形法，但我们的重点将放在算法的直觉理解上，而非记住算法的步骤（这些步骤可以交给计算机来处理！）。

下面是我们将要讨论的内容：

1.  为什么需要单纯形法

1.  从图形解法到代数解法

1.  演示单纯形法如何通过一个简单的例子来工作

这是包含我至今为止为此系列撰写的所有文章的列表链接：

![Jarom Hulet](img/44595b6052adc15ed442ed9da5c7bb33.png)

[Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page-----b2f912e4c6fd--------------------------------)

## 线性规划

[查看列表](https://medium.com/@jarom.hulet/list/linear-programming-fe5c1fba2583?source=post_page-----b2f912e4c6fd--------------------------------)4 篇故事！[](../Images/a98c79af17d5bf4693bde9f4e8ea5d91.png)![](img/30d0f2e268559f848a7316d87b8ffa1e.png)![](img/aafe65ad5cd07aa337c7a2e78dfdad6f.png)

# 为什么需要单纯形法
