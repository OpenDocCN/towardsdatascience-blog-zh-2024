# 喜马拉雅山的死亡事件

> 原文：[https://towardsdatascience.com/death-in-the-himalayas-cdb931228ca7?source=collection_archive---------10-----------------------#2024-01-23](https://towardsdatascience.com/death-in-the-himalayas-cdb931228ca7?source=collection_archive---------10-----------------------#2024-01-23)

## 一个完整的数据可视化项目：使用 Python、D3 和 Illustrator。

[](https://medium.com/@karlahrnndz?source=post_page---byline--cdb931228ca7--------------------------------)[![Karla Hernández](../Images/c28ce71cde4f9dd97bf76f215f66882d.png)](https://medium.com/@karlahrnndz?source=post_page---byline--cdb931228ca7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cdb931228ca7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cdb931228ca7--------------------------------) [Karla Hernández](https://medium.com/@karlahrnndz?source=post_page---byline--cdb931228ca7--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cdb931228ca7--------------------------------) ·20 分钟阅读·2024 年 1 月 23 日

--

![](../Images/b874bb25e7a5380a12de56e8877a4ac4.png)

所有图片由作者提供。

# 学习 D3

我一直打算学习 D3。说实话，D3 一直是我所从事的工作中问题的“杀鸡用牛刀”（因为在那些场景中，数据可视化只是达成目标的手段，而非最终产品）。作为一名 Python 开发者，我常常使用 `matplotlib`、`plotly`、`seaborn`、`pandas`（或 `geopandas`）以及 `bokeh` 等工具来“完成任务”。然而，最近我开始花时间仅仅为了好玩去创建数据可视化，看起来现在正是学习 D3 的绝佳时机。

在这篇文章中，我将展示如何使用 Python、D3 和 Illustrator 创建类似上面图形的五座山峰的图表（[珠穆朗玛峰](https://en.wikipedia.org/wiki/Mount_Everest)、[阿玛达布兰峰](https://en.wikipedia.org/wiki/Ama_Dablam)、[卓奥友峰](https://en.wikipedia.org/wiki/Cho_Oyu)、[洛子峰](https://en.wikipedia.org/wiki/Lhotse) 和 [马纳斯鲁峰](https://en.wikipedia.org/wiki/Manaslu)）。我将介绍：

1.  灵感。

1.  获取数据。

1.  初步数据准备。

1.  选择 5 座山峰进行可视化。

1.  为绘图准备数据。

1.  使用 D3 创建 SVG。

1.  保存 SVG 并导入到 Illustrator 中。

1.  在 Illustrator 中处理 SVG。

1.  添加最后的修饰。

1.  学到的经验教训。
