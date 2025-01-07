# 如果你打算从Pandas转到Polars，必须了解的4个函数

> 原文：[https://towardsdatascience.com/4-functions-to-know-if-you-are-planning-to-switch-from-pandas-to-polars-094a04bb4ec8?source=collection_archive---------2-----------------------#2024-01-11](https://towardsdatascience.com/4-functions-to-know-if-you-are-planning-to-switch-from-pandas-to-polars-094a04bb4ec8?source=collection_archive---------2-----------------------#2024-01-11)

## 本文包含了Pandas和Polars的代码

[](https://sonery.medium.com/?source=post_page---byline--094a04bb4ec8--------------------------------)[![Soner Yıldırım](../Images/c589572e9d1ee176cd4f5a0008173f1b.png)](https://sonery.medium.com/?source=post_page---byline--094a04bb4ec8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--094a04bb4ec8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--094a04bb4ec8--------------------------------) [Soner Yıldırım](https://sonery.medium.com/?source=post_page---byline--094a04bb4ec8--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--094a04bb4ec8--------------------------------) ·阅读时长6分钟·2024年1月11日

--

![](../Images/d4761bd98958427f264f0fb138545cd6.png)

图片来自 [Israel Palacio](https://unsplash.com/@othentikisra?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在 [Unsplash](https://unsplash.com/photos/two-square-blue-led-lights-ImcUkZ72oUs?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

当数据量很大时，Pandas有时会变得难以使用。与大数据集相关的两个主要问题是Pandas进行内存中分析以及创建中间副本。

另一方面，Pandas用户友好的API和丰富的灵活函数选择，使其成为最受欢迎的数据分析和处理库之一。

Polars是Pandas的一个很好的替代方案，尤其是当数据量太大，Pandas难以处理时。Polars的语法介于Pandas和PySpark之间。

在本文中，我们将介绍四个在使用Pandas和Polars进行数据清理、处理和分析时必须掌握的函数。

# 数据

首先，我们当然需要数据来学习这些函数是如何工作的。我准备了示例数据，您可以在我的 [datasets](https://github.com/SonerYldrm/datasets/tree/main) 仓库中下载。我们将在本文中使用的数据集名为“data_polars_practicing.csv”。

让我们从将数据集读入DataFrame开始，DataFrame是Polars和Pandas库中的二维数据结构。

```py
import polars as pl

df_pl =…
```
