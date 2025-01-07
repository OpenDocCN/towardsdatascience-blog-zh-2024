# 如何在 Matplotlib 中创建自定义颜色调色板 — 离散与线性颜色图的解释

> 原文：[https://towardsdatascience.com/how-to-create-custom-color-palettes-in-matplotlib-discrete-vs-linear-colormaps-explained-cfe4c5ba1215?source=collection_archive---------10-----------------------#2024-08-29](https://towardsdatascience.com/how-to-create-custom-color-palettes-in-matplotlib-discrete-vs-linear-colormaps-explained-cfe4c5ba1215?source=collection_archive---------10-----------------------#2024-08-29)

## 可操作的指南，教你如何将自定义颜色应用于个性化你的图表

[](https://medium.com/@radecicdario?source=post_page---byline--cfe4c5ba1215--------------------------------)[![Dario Radečić](../Images/41882a3b30bab9da43d66a59f1df366b.png)](https://medium.com/@radecicdario?source=post_page---byline--cfe4c5ba1215--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cfe4c5ba1215--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cfe4c5ba1215--------------------------------) [Dario Radečić](https://medium.com/@radecicdario?source=post_page---byline--cfe4c5ba1215--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cfe4c5ba1215--------------------------------) ·阅读时间 6 分钟·2024年8月29日

--

![](../Images/0160427ebff247107f4b143178d66c0b.png)

文章缩略图（图片来自作者）

如果有一件事能够让一个好的图表变得更好，那就是颜色的选择。

你可以使用 Matplotlib 将任何一组十六进制颜色代码转换为颜色调色板，本文将向你展示如何操作。你还将了解离散和线性颜色调色板的区别，以及为什么其中一种优于另一种。

如果你想获得与我相同的数据可视化质量，请在继续之前按照本文中的步骤操作：

+   [立即改变这3个关键要素，让你的图表脱颖而出](https://darioradecic.substack.com/p/you-need-to-fix-these-3-things-right)

# 如何在 Matplotlib 中创建自定义颜色图

下面是你需要使用的库：

```py
import numpy as np
import pandas as pd
import matplotlib as mpl
import matplotlib.pyplot as plt
```

Matplotlib 允许你创建两种类型的颜色调色板：

+   **离散的** — 调色板包含有限数量的颜色值。非常适合类别数据，但你需要确保调色板中至少有与类别数量相等的颜色。
