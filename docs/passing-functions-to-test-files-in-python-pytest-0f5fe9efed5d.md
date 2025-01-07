# 在Python Pytest中将函数传递到测试文件

> 原文：[https://towardsdatascience.com/passing-functions-to-test-files-in-python-pytest-0f5fe9efed5d?source=collection_archive---------6-----------------------#2024-05-27](https://towardsdatascience.com/passing-functions-to-test-files-in-python-pytest-0f5fe9efed5d?source=collection_archive---------6-----------------------#2024-05-27)

## PYTHON编程

## 这是一个非常常见的问题，但解决方法非常简单：使用fixture。

[](https://medium.com/@nyggus?source=post_page---byline--0f5fe9efed5d--------------------------------)[![Marcin Kozak](../Images/d7faf62e48ed81dab5d8ad92819fff54.png)](https://medium.com/@nyggus?source=post_page---byline--0f5fe9efed5d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0f5fe9efed5d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0f5fe9efed5d--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--0f5fe9efed5d--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0f5fe9efed5d--------------------------------) ·7分钟阅读·2024年5月27日

--

![](../Images/69fb471736259a118623659a6764455d.png)

Fixtures有助于在测试函数中重用对象，包括函数。图片由[rivage](https://unsplash.com/@sigmund?utm_source=medium&utm_medium=referral)提供，[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

当你使用Pytest进行Python单元测试时，可以通过在`conftest.py`文件中定义的Pytest fixtures将对象传递给测试文件。我用了复数形式，因为你可以定义任意数量的`conftest.py`文件，而它们的位置决定了作用域。

当我刚接触Python单元测试时，我常常想，我该如何定义一个测试辅助函数，并在特定的测试函数中使用它。如果这个函数只在一个测试文件中需要，没问题：只需在这个文件中定义函数，然后就可以使用了。

如果你需要在多个测试文件中使用同一个函数怎么办？一种解决方案是在所有这些文件中重新定义该函数——但这显然违反了[DRY原则（Don’t Repeat Yourself）](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)。那么，如何实现呢？

我注意到很多人都在问这个问题——所以写了这篇文章。你会发现，只要你了解`pytest` fixtures的概念，解决方案其实非常简单，甚至是相当自然的。

# Fixtures

本文并不打算介绍Pytest fixtures。因为我计划写一篇...
