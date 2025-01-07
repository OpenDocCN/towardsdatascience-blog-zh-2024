# 当 Pandas 足够用于数据可视化时，你无需使用 Matplotlib

> 原文：[https://towardsdatascience.com/you-dont-need-matplotlib-when-pandas-is-enough-for-data-visualisation-38d5680197a5?source=collection_archive---------2-----------------------#2024-07-22](https://towardsdatascience.com/you-dont-need-matplotlib-when-pandas-is-enough-for-data-visualisation-38d5680197a5?source=collection_archive---------2-----------------------#2024-07-22)

![](../Images/9938848abaa77e3cc01ac8471429bb99.png)

由作者在 Canva 创建

## 一行代码即可绘制数据，使常规的EDA工作变得更加轻松

[](https://christophertao.medium.com/?source=post_page---byline--38d5680197a5--------------------------------)[![Christopher Tao](../Images/bea1e3c81cc62eb28bdba9275d6b326f.png)](https://christophertao.medium.com/?source=post_page---byline--38d5680197a5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--38d5680197a5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--38d5680197a5--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--38d5680197a5--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--38d5680197a5--------------------------------) ·6 分钟阅读 ·2024年7月22日

--

使用数据可视化库，如 Matplotlib、Seaborn 和 Plotly 是很常见的。然而，在日常的临时EDA分析中，我发现我们可能并不一定需要使用这些库。事实上，Pandas 库本身可能足以完成这些工作。

在本文中，我将介绍如何使用 Pandas Plot 在没有 Matplotlib 的情况下生成常见类型的图表。实际上，Pandas Plot 在背后利用了 Matplotlib 对象。然而，它的便利之处在于，我们可以通过一行代码非常轻松地从数据框中绘制图表。

当然，如果我们仅限于使用 Pandas Plot，还是有很多限制的。因此，我建议仅在足够时使用它。在最后一部分，我还会列举一些 Pandas Plot 无法解决的场景。希望本文对你有所帮助。

## 1\. 示例数据集

![](../Images/8ce2ea55e5678248cfb5c78f0d97ea06.png)

由作者在 Canva 创建

通常，我的文章会从安装库开始。然而，针对 Pandas 我们可以跳过这一部分，因为它太常见了。此外，在大多数情况下……
