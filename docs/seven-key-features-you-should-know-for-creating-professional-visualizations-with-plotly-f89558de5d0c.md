# 使用 Plotly 创建专业可视化的七个关键特性

> 原文：[`towardsdatascience.com/seven-key-features-you-should-know-for-creating-professional-visualizations-with-plotly-f89558de5d0c?source=collection_archive---------9-----------------------#2024-08-06`](https://towardsdatascience.com/seven-key-features-you-should-know-for-creating-professional-visualizations-with-plotly-f89558de5d0c?source=collection_archive---------9-----------------------#2024-08-06)

## 在像领先报纸一样的水平上创建可视化

[](https://amandaiglesiasmoreno.medium.com/?source=post_page---byline--f89558de5d0c--------------------------------)![Amanda Iglesias Moreno](https://amandaiglesiasmoreno.medium.com/?source=post_page---byline--f89558de5d0c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f89558de5d0c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f89558de5d0c--------------------------------) [Amanda Iglesias Moreno](https://amandaiglesiasmoreno.medium.com/?source=post_page---byline--f89558de5d0c--------------------------------)

·Published in [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f89558de5d0c--------------------------------) ·5 min read·Aug 6, 2024

--

![](img/a719c367338799275a5e5d6368fe3be4.png)

[Aleks Dorohovich](https://unsplash.com/es/@doctype) in Unsplash

我们习惯在在线报纸上看到互动可视化，通常会想知道数据记者使用什么工具来创建这些专业外观的可视化。事实上，不需要特殊软件来创建这种类型的可视化；Python 中大多数互动可视化库都具有高度定制化的特性，允许从设计角度创建高质量的可视化。在本文中，我们将逐步展示如何通过对 Plotly 创建的互动可视化进行定制，仅通过少量调整即可显著改善其设计。

# 原始可视化

本文中，我们将使用简单的可视化，比如某一变量的时间演变。要可视化的数据对应于 1800 年至 2020 年的德国人口。首先，我们将使用 Plotly 创建最简单的数据可视化，没有任何定制。随后，我们将逐步解释所有使用的定制内容，并在文章末尾提供改进可视化的代码。
