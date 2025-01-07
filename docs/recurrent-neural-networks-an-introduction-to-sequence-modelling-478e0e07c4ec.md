# 循环神经网络：序列建模简介

> 原文：[https://towardsdatascience.com/recurrent-neural-networks-an-introduction-to-sequence-modelling-478e0e07c4ec?source=collection_archive---------7-----------------------#2024-05-08](https://towardsdatascience.com/recurrent-neural-networks-an-introduction-to-sequence-modelling-478e0e07c4ec?source=collection_archive---------7-----------------------#2024-05-08)

## 循环神经网络是什么，通过图示和实例进行演示

[](https://medium.com/@egorhowell?source=post_page---byline--478e0e07c4ec--------------------------------)[![Egor Howell](../Images/1f796e828f1625440467d01dcc3e40cd.png)](https://medium.com/@egorhowell?source=post_page---byline--478e0e07c4ec--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--478e0e07c4ec--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--478e0e07c4ec--------------------------------) [Egor Howell](https://medium.com/@egorhowell?source=post_page---byline--478e0e07c4ec--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--478e0e07c4ec--------------------------------) ·阅读时间9分钟·2024年5月8日

--

![](../Images/ee76515d7e9c035ad7f216454da34a6b.png)

”[https://www.flaticon.com/free-icons/connection](https://www.flaticon.com/free-icons/connection)" title="连接图标">连接图标由Freepik创建 — Flaticon.

许多问题和现象是基于序列的。常见的例子包括语音、天气模式和时间序列。这些系统的下一个状态取决于先前的状态。

不幸的是，传统的神经网络无法处理或预测这种类型的数据，因为它们将输入数据孤立地进行分析。它们没有意识到数据实际上是有序列性的。

*那么，我们如何预测这种类型的数据呢？*

好的，我们转向一种叫做循环神经网络（RNN）的技术！

> **注意：** 确实存在一些技术方法和技巧，可以使传统的神经网络与序列数据一起工作。但这就像试图把一只大象塞进鞋盒里——根本行不通！

如果你不熟悉标准的神经网络，我有一系列博客供你参考！我建议你在深入了解循环神经网络之前，先熟悉一下这些常规神经网络是如何工作的。
