# 在 Python 中覆盖对象：棘手、危险且强大

> 原文：[`towardsdatascience.com/overwriting-in-python-tricky-dangerous-powerful-04b12a9b1a7e?source=collection_archive---------3-----------------------#2024-04-10`](https://towardsdatascience.com/overwriting-in-python-tricky-dangerous-powerful-04b12a9b1a7e?source=collection_archive---------3-----------------------#2024-04-10)

## PYTHON 编程

## 虽然覆盖对象是 Python 编程中的典型技巧，但它可能会导致意想不到的效果。你需要了解如何使用它，才能发挥其优势。

[](https://medium.com/@nyggus?source=post_page---byline--04b12a9b1a7e--------------------------------)![Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--04b12a9b1a7e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--04b12a9b1a7e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--04b12a9b1a7e--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--04b12a9b1a7e--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--04b12a9b1a7e--------------------------------) ·27 分钟阅读·2024 年 4 月 10 日

--

![](img/24603710e2708d773ede8bac7ae78647.png)

在 Python 中覆盖对象可能是危险的：不要在未深思熟虑的情况下进行操作。照片由[Raúl Nájera](https://unsplash.com/@reinf?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我曾经覆盖过许多不同的对象。如果你也曾在 Python 中编写过代码，可能也有过类似的经历。这是因为，在 Python 中，覆盖对象是语言的核心特性之一。

我不仅覆盖了变量，还覆盖了函数、类和类方法——甚至是异常：

[](https://betterprogramming.pub/how-to-overwrite-asserterror-in-python-and-use-custom-exceptions-c0b252989977?source=post_page-----04b12a9b1a7e--------------------------------) [## 如何在 Python 中覆盖 AssertionError 并使用自定义异常

### Python 的 assert 语句使用的是 AssertionError。了解如何使用其他异常来代替它。

betterprogramming.pub](https://betterprogramming.pub/how-to-overwrite-asserterror-in-python-and-use-custom-exceptions-c0b252989977?source=post_page-----04b12a9b1a7e--------------------------------)

事实上，我们会区分覆盖变量和可调用对象。这两者之间的区别实际上相当重要，后者更为棘手。尽管我们的重点是覆盖可调用对象，因为它是一种更高级的技巧，我们也会讨论覆盖变量，因为它为我们的讨论提供了一个良好的起点。
