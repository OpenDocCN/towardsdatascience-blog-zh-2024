# 仿真数据，真实学习：功效分析

> 原文：[https://towardsdatascience.com/simulated-data-real-learnings-power-analysis-652045eeae22?source=collection_archive---------8-----------------------#2024-03-26](https://towardsdatascience.com/simulated-data-real-learnings-power-analysis-652045eeae22?source=collection_archive---------8-----------------------#2024-03-26)

## 第2部分 — 实验功效分析

[](https://medium.com/@jarom.hulet?source=post_page---byline--652045eeae22--------------------------------)[![Jarom Hulet](../Images/0fdeb1a2df90cccdd8f2f4b84d5e54eb.png)](https://medium.com/@jarom.hulet?source=post_page---byline--652045eeae22--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--652045eeae22--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--652045eeae22--------------------------------) [Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page---byline--652045eeae22--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--652045eeae22--------------------------------) ·阅读时间 11 分钟 ·2024年3月26日

--

![](../Images/f2e441f19ba6c23cc6e9bfc2e546691c.png)

图片由 Robert So 提供，来自 Pexels.com

**引言**

仿真是数据科学工具箱中的一个强大工具。阅读本文后，您将对如何使用仿真来估算实验设计的效能有一个很好的理解。这是一个多部分系列文章的第二部分，讨论了仿真在数据科学和机器学习中的应用。

以下是我们将要涵盖的内容：

1.  功效分析概述

1.  如何使用仿真计算功效 — 基于示例的方法

在本文中，我将简要介绍数据仿真的定义：

> 数据仿真是创造虚拟数据，模拟真实世界的属性。

在本系列的第1部分中，我将更广泛地讨论数据仿真的定义——您可以通过以下链接查看：

[](/simulated-data-real-learnings-part-1-dccb52667e32?source=post_page-----652045eeae22--------------------------------) [## 仿真数据，真实学习：第1部分

### 使用仿真测试机器学习方法

[towardsdatascience.com](/simulated-data-real-learnings-part-1-dccb52667e32?source=post_page-----652045eeae22--------------------------------)

**功效分析概述**
