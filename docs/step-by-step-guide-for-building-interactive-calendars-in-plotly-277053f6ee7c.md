# Plotly 中交互式日历的逐步构建指南

> 原文：[https://towardsdatascience.com/step-by-step-guide-for-building-interactive-calendars-in-plotly-277053f6ee7c?source=collection_archive---------9-----------------------#2024-08-16](https://towardsdatascience.com/step-by-step-guide-for-building-interactive-calendars-in-plotly-277053f6ee7c?source=collection_archive---------9-----------------------#2024-08-16)

## 使用 Plotly 创建带热图的交互式日历

[](https://amandaiglesiasmoreno.medium.com/?source=post_page---byline--277053f6ee7c--------------------------------)[![Amanda Iglesias Moreno](../Images/9cff03d0c0caaa9a8aa53c4c34c90cac.png)](https://amandaiglesiasmoreno.medium.com/?source=post_page---byline--277053f6ee7c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--277053f6ee7c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--277053f6ee7c--------------------------------) [Amanda Iglesias Moreno](https://amandaiglesiasmoreno.medium.com/?source=post_page---byline--277053f6ee7c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--277053f6ee7c--------------------------------) ·阅读时长 7 分钟·2024年8月16日

--

![](../Images/f2a5033f07429bdc68baacb410c05865.png)

[**Debby Hudson**](https://unsplash.com/es/@hudsoncrafted) **在 Unsplash**

Plotly 是最全面的 Python 可视化库之一。这个库提供了许多预定义的可视化工具，适用于大多数数据分析。这些可视化工具高度可定制，使我们能够更具创造性地展示我们的见解，甚至实现新的可视化效果。

在本文中，我们将逐步讲解如何使用 Plotly 创建一个日历，展示巴塞罗那 2024 年的所有假期。该日历将具有交互性，通过悬停提示提供有关城市中不同假期的信息。正如文章所示，尽管 Plotly 没有内置的日历可视化工具，但从热图开始，稍加想象，这种类型的可视化可以轻松实现。

Plotly 中的日历相比预定义工具所创建的日历具有更高的自定义性。此外，一个主要优势是，它们可以集成到 Jupyter 笔记本中，例如，可以在这些笔记本中进行时间分析。此外，悬停提示可以根据我们的分析需求进行自定义。与预定义工具相比，另一个重要的优势是 Plotly 完全免费……
