# 如何处理：时间序列的缺失数据

> 原文：[https://towardsdatascience.com/how-to-handle-missing-data-for-time-series-680810f648ed?source=collection_archive---------3-----------------------#2024-05-25](https://towardsdatascience.com/how-to-handle-missing-data-for-time-series-680810f648ed?source=collection_archive---------3-----------------------#2024-05-25)

## 你应该丢弃、插值还是填充？

[](https://medium.com/@pelletierhaden?source=post_page---byline--680810f648ed--------------------------------)[![Haden Pelletier](../Images/8f73fc8222e783883c4ebcaee14513e0.png)](https://medium.com/@pelletierhaden?source=post_page---byline--680810f648ed--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--680810f648ed--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--680810f648ed--------------------------------) [Haden Pelletier](https://medium.com/@pelletierhaden?source=post_page---byline--680810f648ed--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--680810f648ed--------------------------------) ·阅读时长5分钟·2024年5月25日

--

![](../Images/229268897c4d0e5ec19aa8f51bcf62d0.png)

图片来源：[Mika Baumeister](https://unsplash.com/@kommumikation?utm_source=medium&utm_medium=referral)来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

没有完美的数据集。每个数据科学家在数据探索时都有过这种感觉，当他们执行以下操作时：

```py
df.info()
```

并且看到如下内容：

![](../Images/560f6e43bf14b6389f0dcfd2f2b5312c.png)

[UCI机器学习库空气质量数据集](https://archive.ics.uci.edu/dataset/360/air+quality)信息（CC BY 4.0）。图片由作者提供

大多数机器学习模型无法处理NaN或空值，因此，如果你的特征或目标变量中包含这些值，在尝试拟合模型之前，必须适当处理它们。

在本文中，我将探讨三种处理时间序列数据集中空值/缺失数据的简单方法。

# 1\. 丢弃空值

这可能是处理缺失数据最简单、最直接的方法：直接丢弃它。

```py
# Drop any and all nulls across all columns
df.dropna(inplace=True)
```

默认情况下，pandas的[dropna](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.dropna.html)函数会在所有列中查找空值，并删除**任何行**，只要其中**任何一列**存在空值。然而，可以通过使用各种参数来修改这一行为。
