# 逻辑回归的视觉理解

> 原文：[`towardsdatascience.com/a-visual-understanding-of-logistic-regression-2e6733844397?source=collection_archive---------5-----------------------#2024-05-09`](https://towardsdatascience.com/a-visual-understanding-of-logistic-regression-2e6733844397?source=collection_archive---------5-----------------------#2024-05-09)

## 逻辑回归背后数学原理的直观视觉解释

[](https://reza-bagheri79.medium.com/?source=post_page---byline--2e6733844397--------------------------------)![Reza Bagheri](https://reza-bagheri79.medium.com/?source=post_page---byline--2e6733844397--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2e6733844397--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2e6733844397--------------------------------) [Reza Bagheri](https://reza-bagheri79.medium.com/?source=post_page---byline--2e6733844397--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2e6733844397--------------------------------)·15 分钟阅读·2024 年 5 月 9 日

--

![](img/933b293714fa9e14d9cf5dde5f99236b.png)

图片由 DALL.E 生成

逻辑回归是一种用于二分类的统计模型。在二分类问题中，目标只有两个类别，因此机器学习算法应将数据分类到这两个类别之一。逻辑回归的名称源自逻辑函数，该函数用于预测一个数据点属于每个类别的概率。逻辑回归在监督学习、金融、医学和社会科学等领域有广泛的应用。

在本文中，我将展示对逻辑回归的视觉理解，以及模型中每个元素的作用。阅读本文后，读者可以对逻辑回归及其局限性有直观的理解。

*本文中的所有图片均由作者创建。*

**一个玩具数据集**

我们创建了一个玩具数据集，以演示逻辑回归如何解决分类问题。首先，我们导入所有需要的 Python 库。

```py
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

from sklearn.linear_model import LogisticRegression
from matplotlib.colors import ListedColormap

import…
```
