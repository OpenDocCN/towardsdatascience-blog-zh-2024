# 向 Plotly 图表添加渐变背景。

> 原文：[https://towardsdatascience.com/adding-gradient-backgrounds-to-plotly-charts-1dec85dd3227?source=collection_archive---------10-----------------------#2024-10-08](https://towardsdatascience.com/adding-gradient-backgrounds-to-plotly-charts-1dec85dd3227?source=collection_archive---------10-----------------------#2024-10-08)

## 使用 Plotly 矩形形状来改善数据可视化。

[](https://andymcdonaldgeo.medium.com/?source=post_page---byline--1dec85dd3227--------------------------------)[![Andy McDonald](../Images/df11d647be032aeb3d31852affb33a64.png)](https://andymcdonaldgeo.medium.com/?source=post_page---byline--1dec85dd3227--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1dec85dd3227--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1dec85dd3227--------------------------------) [Andy McDonald](https://andymcdonaldgeo.medium.com/?source=post_page---byline--1dec85dd3227--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1dec85dd3227--------------------------------) ·5 分钟阅读·2024年10月8日

--

![](../Images/233463b5f4f084fab3cc1d1466b8b9c4.png)

使用 Plotly Express 绘制带有渐变背景的折线图，强调高温和低温。图片来源：作者。

向图表的背景添加渐变或分段可以帮助提高你所展示信息的清晰度。它可以帮助吸引注意力到高温或低温区域，或者用于识别某个测量值是否超出公差范围。例如，如果 LWD 井下测量工具的振动超过该工具的规格限制，或者它可以用于温度数据，突出显示热区和冷区。

在本文中，我将分享如何通过简单的代码，为 Plotly Express 折线图添加渐变背景。

# 创建数据

在开始绘制图表之前，我们首先需要创建一个数据集。在这个示例中，我使用的是随机生成的温度数据，跨越一段时间。

首先，我们需要导入将要使用的库。

+   **plotly express** 将被用来生成我们的图表。

+   **matplotlib** 将被用来生成我们的颜色图并将选定的值转换为十六进制。

+   **numpy** 将被用来帮助创建我们的颜色刻度。

```py
import plotly.express as px
import matplotlib.pyplot as plt…
```
