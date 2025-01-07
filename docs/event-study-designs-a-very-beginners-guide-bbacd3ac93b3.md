# 事件研究设计：初学者指南

> 原文：[`towardsdatascience.com/event-study-designs-a-very-beginners-guide-bbacd3ac93b3?source=collection_archive---------6-----------------------#2024-07-20`](https://towardsdatascience.com/event-study-designs-a-very-beginners-guide-bbacd3ac93b3?source=collection_archive---------6-----------------------#2024-07-20)

## 它们是什么，它们又不是什麼

[](https://medium.com/@arieda.muco?source=post_page---byline--bbacd3ac93b3--------------------------------)![Arieda Muço](https://medium.com/@arieda.muco?source=post_page---byline--bbacd3ac93b3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bbacd3ac93b3--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bbacd3ac93b3--------------------------------) [Arieda Muço](https://medium.com/@arieda.muco?source=post_page---byline--bbacd3ac93b3--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bbacd3ac93b3--------------------------------) ·阅读时长 8 分钟·2024 年 7 月 20 日

--

在本文中，我尝试澄清应用计量经济学家的工具箱中的基本工具：差异中的差异（DiD）和事件研究设计。本文主要受到我的学生们的启发，简明地介绍了基本概念，并解决了常见的误解，这些误解经常让实践者感到困惑。

如果你想知道为什么标题专注于事件研究，而我又在谈论 DiD，那是因为在因果推断方面，事件研究是差异中的差异的一个推广。

但在深入探讨之前，请让我向你保证，如果你感到困惑，可能是有其正当理由的。近年来，DiD 文献的快速发展带来了许多新的方法论，使得跟上其步伐变得具有挑战性。事件研究设计的起源也未必能帮助澄清这些问题……

# 事件研究的起源

## 金融学的起点

事件研究起源于金融学，旨在评估特定事件（如财报公告或并购）对股价的影响。事件研究由 Ball 和 Brown（1968）开创，为该方法奠定了基础。

# 财务中的事件研究

## 方法论

在金融学中，事件研究方法论涉及识别一个事件窗口，用于衡量“异常收益”，即…
