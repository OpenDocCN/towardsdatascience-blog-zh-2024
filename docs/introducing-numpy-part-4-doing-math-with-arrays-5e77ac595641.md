# 介绍NumPy，第4部分：使用数组进行数学运算

> 原文：[https://towardsdatascience.com/introducing-numpy-part-4-doing-math-with-arrays-5e77ac595641?source=collection_archive---------13-----------------------#2024-09-16](https://towardsdatascience.com/introducing-numpy-part-4-doing-math-with-arrays-5e77ac595641?source=collection_archive---------13-----------------------#2024-09-16)

## 快速成功数据科学

## 还有读取和写入数组数据！

[](https://medium.com/@lee_vaughan?source=post_page---byline--5e77ac595641--------------------------------)[![Lee Vaughan](../Images/9f6b90bb76102f438ab0b9a4a62ffa3f.png)](https://medium.com/@lee_vaughan?source=post_page---byline--5e77ac595641--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5e77ac595641--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--5e77ac595641--------------------------------) [Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--5e77ac595641--------------------------------)

·发表于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--5e77ac595641--------------------------------) ·11分钟阅读·2024年9月16日

--

![](../Images/99e5016cd06af6e82b0228cb5a843e79.png)

由DALL-E3想象的进行数学运算的数组

欢迎来到初学者系列的第四篇也是最后一篇，*介绍NumPy*! 在前几篇文章中，我们回顾了NumPy的主力阵容——*数组*：它们是什么以及如何创建它们（[第1部分](https://medium.com/towards-data-science/introducing-numpy-part-1-understanding-arrays-3f6fecc97e3d)）；如何索引和切片它们（[第2部分](https://medium.com/towards-data-science/introducing-numpy-part-2-indexing-arrays-5b381b90d1d0)）；以及如何操作它们（[第3部分](https://medium.com/towards-data-science/introducing-numpy-part-3-manipulating-arrays-2685f5d3299d)）。现在是时候将它们应用到它们的主要目的上：数学运算了。

NumPy使用两种内部实现来高效地在数组上执行数学运算：*向量化*和*广播*。向量化支持对*相同大小*数组的操作，而广播则将这一行为扩展到*不同形状*的数组。

# 向量化

`ndarrays`的一个最强大功能是向量化，它让你可以在数据上执行批量操作，而无需显式的`for`循环。这意味着你可以一次性对整个数组应用操作，而无需逐个选择其中的元素。

算术运算对于相同大小的数组是*逐元素*应用的，如下图所示：

由于循环是在后台通过C语言实现的代码进行的，向量化可以带来更快的处理速度。让我们来看一个例子，…
