# 介绍 NumPy，第三部分：操作数组

> 原文：[`towardsdatascience.com/introducing-numpy-part-3-manipulating-arrays-2685f5d3299d?source=collection_archive---------2-----------------------#2024-09-15`](https://towardsdatascience.com/introducing-numpy-part-3-manipulating-arrays-2685f5d3299d?source=collection_archive---------2-----------------------#2024-09-15)

## 数组的形状、转置、连接与拆分

[](https://medium.com/@lee_vaughan?source=post_page---byline--2685f5d3299d--------------------------------)![Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--2685f5d3299d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2685f5d3299d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2685f5d3299d--------------------------------) [Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--2685f5d3299d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2685f5d3299d--------------------------------) ·阅读时间 8 分钟·2024 年 9 月 15 日

--

![](img/42ed35b32f19b5781cd0dca5990f9d4d.png)

DALL-E3 想象的数组操作

欢迎来到*介绍 NumPy*系列的第三部分，这是为那些新接触这个必备 Python 库的人准备的入门教程。[第一部分](https://medium.com/towards-data-science/introducing-numpy-part-1-understanding-arrays-3f6fecc97e3d)介绍了 NumPy 数组以及如何创建它们。[第二部分](https://medium.com/towards-data-science/introducing-numpy-part-2-indexing-arrays-5b381b90d1d0)讲解了数组的索引和切片。第三部分将展示如何通过重塑数组、交换轴、以及合并和拆分数组来操作现有的数组。这些操作对于旋转、放大、平移图像以及拟合机器学习模型等任务非常有用。

# 形状变化与转置

NumPy 提供了多种方法来改变数组的形状、转置数组（交换列和行）、以及交换轴。你已经在本系列中使用过`reshape()`方法。

使用`reshape()`时需要注意的一点是，和所有 NumPy 的赋值操作一样，它创建的是数组的*视图*而不是*副本*。在下面的示例中，重塑`arr1d`数组仅会对数组进行临时更改：

```py
In [1]: import numpy as np

In [2]: arr1d = np.array([1, 2, 3, 4])

In [3]: arr1d.reshape(2, 2)
Out[3]: 
array([[1, 2],
       [3, 4]])

In [4]: arr1d
Out[4]: array([1, 2, 3, 4])
```

当你想要*临时*改变数组的形状以便在某些操作中使用时，这种行为非常有用…
