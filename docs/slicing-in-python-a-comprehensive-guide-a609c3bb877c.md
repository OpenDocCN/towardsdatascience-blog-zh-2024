# Python中的切片：全面指南

> 原文：[https://towardsdatascience.com/slicing-in-python-a-comprehensive-guide-a609c3bb877c?source=collection_archive---------7-----------------------#2024-06-05](https://towardsdatascience.com/slicing-in-python-a-comprehensive-guide-a609c3bb877c?source=collection_archive---------7-----------------------#2024-06-05)

## PYTHON编程

## 精通Python切片：从列表和元组到NumPy数组和Pandas数据框架，以及自定义实现

[](https://medium.com/@nyggus?source=post_page---byline--a609c3bb877c--------------------------------)[![Marcin Kozak](../Images/d7faf62e48ed81dab5d8ad92819fff54.png)](https://medium.com/@nyggus?source=post_page---byline--a609c3bb877c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a609c3bb877c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a609c3bb877c--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--a609c3bb877c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a609c3bb877c--------------------------------) ·30分钟阅读·2024年6月5日

--

![](../Images/a299a7f2ddca0d135db0b57f4edf96ad.png)

图片由[freestocks](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

Python提供了多种数据容器，其中最常见的是列表。从更高的层次来看，它是一个将对象一个接一个地存储在容器中的序列。因此，我们需要一种机制来访问这样的容器中的特定元素——这个机制被称为*索引*。它的工作方式如下所示，且对列表和元组来说是完全相同的：

```py
>>> objects = [
...     'Zen',
...     float('nan'),
...     25,
...     None,
...     ValueError,
...     ValueError(),
... ]
>>> objects[0]
'Zen'
>>> objects[1]
nan
>>> objects[-1]
ValueError()
>>> objects[-2]
<class 'ValueError'>
```

索引使得我们能够访问容器中的单个元素——然而，我们通常需要访问不止一个元素。实现这一目标的机制被称为*slicing*（切片）。

切片是Python中最强大且最方便的功能之一，它使得我们能够访问和操作序列的部分内容——如列表、元组、字符串、数组和数据框架，当然也包括自定义序列。在数据科学中，切片是你需要掌握的关键工具之一，因为……
