# 不要让 Python 的 `dir()` 函数欺骗你！

> 原文：[https://towardsdatascience.com/dont-let-the-python-dir-function-trick-you-3c42abbdb4d9?source=collection_archive---------8-----------------------#2024-05-13](https://towardsdatascience.com/dont-let-the-python-dir-function-trick-you-3c42abbdb4d9?source=collection_archive---------8-----------------------#2024-05-13)

## PYTHON 编程

## `dir()` 函数返回对象的属性。不幸的是，它并不会显示所有内容——发现如何查看所有属性吧。

[](https://medium.com/@nyggus?source=post_page---byline--3c42abbdb4d9--------------------------------)[![Marcin Kozak](../Images/d7faf62e48ed81dab5d8ad92819fff54.png)](https://medium.com/@nyggus?source=post_page---byline--3c42abbdb4d9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3c42abbdb4d9--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3c42abbdb4d9--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--3c42abbdb4d9--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3c42abbdb4d9--------------------------------) ·阅读时间：7分钟·2024年5月13日

--

![](../Images/34d868b4f1fd437161b2bbcf9aff6fa6.png)

这真的是所有内容吗？`dir()` 函数并不保证显示所有内容！照片由 [Cristiano Pinto](https://unsplash.com/@crispinto?utm_source=medium&utm_medium=referral) 提供，来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

这是 Python 的内建 `dir()` 函数：

![](../Images/e878a5a64c336cb728aa0e9efd0be94f.png)

`dir()` 函数的帮助。图片来源：作者

简而言之，当用于某个对象，比如 `obj` 时，函数返回的是其属性的名称……或者严格来说，*部分*属性——这个*部分*差异可不小。

如果你在[互联网上](https://www.google.com/search?q=python+dir+does+not+list+all+attributes)搜索，你会发现有不少关于`dir()`问题的讨论。在[StackOverflow](https://stackoverflow.com/)上，你会找到关于这个问题的各种Python对象的讨论，比如[Outlook邮件](https://stackoverflow.com/questions/19564186/dir-and-help-not-showing-all-attributes-of-an-object-in-python)、`[json](https://stackoverflow.com/questions/57576522/python-object-dict-not-showing-all-attributes)` [对象](https://stackoverflow.com/questions/57576522/python-object-dict-not-showing-all-attributes)、[解析过的XML对象](https://stackoverflow.com/questions/22105701/python-method-dir-does-not-return-all-attributes-methods)，以及`[scipy.sparse](https://stackoverflow.com/questions/36211996/why-dir-doesnt-show-all-python-object-attributes)` [对象](https://stackoverflow.com/questions/36211996/why-dir-doesnt-show-all-python-object-attributes)。显然，人们期望`dir()`返回所有的属性。在研究这个话题之前，我也有这样的期望——所以我能理解这种困惑。

即使你确实阅读了函数的文档字符串并注意到“某些属性”这个短语，它也无法帮助你找到所有的属性。
