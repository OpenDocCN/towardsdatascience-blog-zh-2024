# 优化 Pandas 代码：操作顺序的影响

> 原文：[`towardsdatascience.com/optimizing-pandas-code-the-impact-of-operation-sequence-0c5aa159632a?source=collection_archive---------2-----------------------#2024-03-18`](https://towardsdatascience.com/optimizing-pandas-code-the-impact-of-operation-sequence-0c5aa159632a?source=collection_archive---------2-----------------------#2024-03-18)

## PYTHON 编程

## 学习如何重排代码以实现显著的速度提升。

[](https://medium.com/@nyggus?source=post_page---byline--0c5aa159632a--------------------------------)![Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--0c5aa159632a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0c5aa159632a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0c5aa159632a--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--0c5aa159632a--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0c5aa159632a--------------------------------) ·阅读时间 9 分钟·2024 年 3 月 18 日

--

![](img/a7f221c06371b40b5dab7ff3281318ad.png)

图片来自[Nick Fewings](https://unsplash.com/@jannerboy62?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Pandas 提供了一个出色的数据框操作框架。在数据科学中，我们处理的可能是小型、大型——有时是非常大的数据框。分析小型数据框可能非常迅速，但即使对一个大型数据框进行单一操作，也可能需要相当长的时间。

在本文中，我将展示如何通过一个几乎不需要成本的操作：调整数据框的操作顺序，来缩短这段时间。

假设我们有如下的数据框（dataframe）：

```py
import pandas as pd

n = 1_000_000
df = pd.DataFrame({
    letter: list(range(n))
    for letter in "abcdefghijklmnopqrstuwxyz"
})
```

拥有一百万行和 25 列的数据框非常庞大。在当前的个人计算机上，对这样的数据框进行多次操作会显著影响性能。

假设我们想要筛选行，满足以下条件：`a < 50_000 且 b > 3000`，并选择五个列：`take_cols=['a', 'b', 'g', 'n', 'x']`。我们可以通过以下方式实现：

```py
subdf = df[take_cols]
subdf = subdf[subdf['a'] < 50_000]
subdf = subdf[subdf['b'] > 3000]
```
