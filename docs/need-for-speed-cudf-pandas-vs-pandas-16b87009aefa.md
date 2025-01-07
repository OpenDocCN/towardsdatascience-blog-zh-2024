# 速度需求：cuDF Pandas vs. Pandas

> 原文：[https://towardsdatascience.com/need-for-speed-cudf-pandas-vs-pandas-16b87009aefa?source=collection_archive---------4-----------------------#2024-04-05](https://towardsdatascience.com/need-for-speed-cudf-pandas-vs-pandas-16b87009aefa?source=collection_archive---------4-----------------------#2024-04-05)

![](../Images/70932d5df5e198c49c910589e638d218.png)

图片由作者提供（Dalle-3）

## 比较概述

[](https://medium.com/@thomas_reid?source=post_page---byline--16b87009aefa--------------------------------)[![Thomas Reid](../Images/c1b4e5f577272633ba07e5dbfd21c02d.png)](https://medium.com/@thomas_reid?source=post_page---byline--16b87009aefa--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--16b87009aefa--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--16b87009aefa--------------------------------) [Thomas Reid](https://medium.com/@thomas_reid?source=post_page---byline--16b87009aefa--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--16b87009aefa--------------------------------) ·阅读时间 11 分钟·2024 年 4 月 5 日

--

## 什么是 cuDF Pandas？

如果你是 Python 中 Pandas 库的用户，并且希望或需要最大化程序的运行时间，那么你有几个可供选择的选项。这些选项大多涉及使用外部库，取代现有的 Pandas 操作，并对大规模、高速数据处理进行了优化。此类库的例子包括 VAEX、POLARS、DuckDB 等。问题在于，这些库通常要求你在某种程度上重写代码，这可能不是你想做的，或者你没有能力去做。

如果你的系统幸运地配有 GPU，那么另一个更为近期的可选项就是 `cuDF.pandas`。

cuDF.pandas 构建于 cuDF 之上，它是一个基于 GPU 的 Python DataFrame 库（基于 [Apache Arrow](http://arrow.apache.org/) 列存储内存格式），用于加载、连接、聚合、过滤以及其他数据操作。

要使用 cuDF Pandas，你只需在命令行运行 Python 时提供一个标志，或者在通过 Jupyter Notebook 运行 Python 时加载一个扩展。当支持 GPU 计算时（例如，有 NVIDIA GPU 可用，且 cuDF 知道如何运行 Pandas 代码），你的代码将在 GPU 上运行。在无法支持的情况下，cuDF…
