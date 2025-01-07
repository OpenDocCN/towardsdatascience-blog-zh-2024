# 掌握Python丰富图表库的多样性与深度（附代码）

> 原文：[https://towardsdatascience.com/mastering-the-versatility-and-depth-of-pythons-rich-plot-collection-with-code-b136b584d143?source=collection_archive---------2-----------------------#2024-03-30](https://towardsdatascience.com/mastering-the-versatility-and-depth-of-pythons-rich-plot-collection-with-code-b136b584d143?source=collection_archive---------2-----------------------#2024-03-30)

## 从*Sankey图*和*蜘蛛图*到*山脊图*，本文涵盖了各种可视化，每种都有其独特的应用场景和洞见。

[](https://theomitsa.medium.com/?source=post_page---byline--b136b584d143--------------------------------)[![Dr. Theophano Mitsa](../Images/a39dfae5f4409120b840cd9182b148c6.png)](https://theomitsa.medium.com/?source=post_page---byline--b136b584d143--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b136b584d143--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b136b584d143--------------------------------) [Dr. Theophano Mitsa](https://theomitsa.medium.com/?source=post_page---byline--b136b584d143--------------------------------)

·发布于[数据科学之路](https://towardsdatascience.com/?source=post_page---byline--b136b584d143--------------------------------) ·11分钟阅读·2024年3月30日

--

![](../Images/5209aeaf5bf9d1ca1a50aadbedd3cb56.png)

图片由作者在DALL-E创建

> “**让数据集改变你的思维方式。** —— 汉斯·罗斯林”

每个数据科学家都知道图表在其数据故事中至关重要。Python开发者幸运地能使用一门提供丰富图表库的语言。本文将通过讨论一些较少见的可视化应用场景，如*Sankey图*、*山脊图*、*嵌套图、蜘蛛图*和*词云图*，来展示这种丰富性。我们还将讨论一些更常见的图形表示方法，如*散点图*和*条形图*。大多数图表将使用*Matplotlib*、*Seaborn*和*Plotly* Python库。

我们将使用诸如形状、大小、颜色、方向、面积大小和标记符号区域等属性，创建十个不同应用场景的图表。在每个应用场景中，我们的目标是创建有效、高效且具有美感的可视化。让我们描述在图表上下文中这些词语的含义：(a) 有效**:** 所有需要传达的信息都包含在图表中 (b) 高效：图表中没有冗余数据 (c) 美学…
