# 如果你打算从 Pandas 转到 Polars，必须了解的 4 个函数

> 原文：[`towardsdatascience.com/4-functions-to-know-if-you-are-planning-to-switch-from-pandas-to-polars-094a04bb4ec8?source=collection_archive---------2-----------------------#2024-01-11`](https://towardsdatascience.com/4-functions-to-know-if-you-are-planning-to-switch-from-pandas-to-polars-094a04bb4ec8?source=collection_archive---------2-----------------------#2024-01-11)

## 本文包含了 Pandas 和 Polars 的代码

[](https://sonery.medium.com/?source=post_page---byline--094a04bb4ec8--------------------------------)![Soner Yıldırım](https://sonery.medium.com/?source=post_page---byline--094a04bb4ec8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--094a04bb4ec8--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--094a04bb4ec8--------------------------------) [Soner Yıldırım](https://sonery.medium.com/?source=post_page---byline--094a04bb4ec8--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--094a04bb4ec8--------------------------------) ·阅读时长 6 分钟·2024 年 1 月 11 日

--

![](img/d4761bd98958427f264f0fb138545cd6.png)

图片来自 [Israel Palacio](https://unsplash.com/@othentikisra?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 在 [Unsplash](https://unsplash.com/photos/two-square-blue-led-lights-ImcUkZ72oUs?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

当数据量很大时，Pandas 有时会变得难以使用。与大数据集相关的两个主要问题是 Pandas 进行内存中分析以及创建中间副本。

另一方面，Pandas 用户友好的 API 和丰富的灵活函数选择，使其成为最受欢迎的数据分析和处理库之一。

Polars 是 Pandas 的一个很好的替代方案，尤其是当数据量太大，Pandas 难以处理时。Polars 的语法介于 Pandas 和 PySpark 之间。

在本文中，我们将介绍四个在使用 Pandas 和 Polars 进行数据清理、处理和分析时必须掌握的函数。

# 数据

首先，我们当然需要数据来学习这些函数是如何工作的。我准备了示例数据，您可以在我的 [datasets](https://github.com/SonerYldrm/datasets/tree/main) 仓库中下载。我们将在本文中使用的数据集名为“data_polars_practicing.csv”。

让我们从将数据集读入 DataFrame 开始，DataFrame 是 Polars 和 Pandas 库中的二维数据结构。

```py
import polars as pl

df_pl =…
```
