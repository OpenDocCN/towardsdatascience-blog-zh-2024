# 使你的 Matplotlib 图表达到出版水平的 3 个关键调整

> 原文：[https://towardsdatascience.com/3-key-tweaks-that-will-make-your-matplotlib-charts-publication-ready-18e41eaac589?source=collection_archive---------3-----------------------#2024-08-08](https://towardsdatascience.com/3-key-tweaks-that-will-make-your-matplotlib-charts-publication-ready-18e41eaac589?source=collection_archive---------3-----------------------#2024-08-08)

## Matplotlib 图表默认看起来很难看——这是该怎么解决。

[](https://medium.com/@radecicdario?source=post_page---byline--18e41eaac589--------------------------------)[![Dario Radečić](../Images/41882a3b30bab9da43d66a59f1df366b.png)](https://medium.com/@radecicdario?source=post_page---byline--18e41eaac589--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--18e41eaac589--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--18e41eaac589--------------------------------) [Dario Radečić](https://medium.com/@radecicdario?source=post_page---byline--18e41eaac589--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--18e41eaac589--------------------------------) ·阅读时间 5 分钟·2024年8月8日

--

![](../Images/4800e476202ff22aec05ee97f5e4d5c2.png)

文章缩略图（作者提供的图片）

数据可视化比直接查看原始数值数据提供了更深刻的见解。

然而，创建吸引人的图表需要时间和精力。Matplotlib 是 Python 数据可视化的事实标准库。它简单，已经使用了几十年，任何你想要的功能只需一次网络搜索就能找到。

但并非一切都那么美好和顺利。**Matplotlib 的默认可视化效果非常糟糕**，作为数据专业人士，你将需要调整许多参数才能得到可用的效果。今天的文章目标就是帮助你做到这一点。

到最后，你将拥有一段可以粘贴到任何 Jupyter Notebook 的代码片段。

# Matplotlib 默认样式的问题是什么？

为了跟随本教程，你无需下载任何数据集。你将创建一个合成的时间序列数据集，具有上升趋势和可重复的季节性模式：

```py
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# Single season multiplier factors - for seasonality effect
seasonal_multipliers = [1.1, 1.3, 1.2, 1.5, 1.9, 2.3, 2.1, 2.8, 2.0, 1.7, 1.5, 1.2]…
```
