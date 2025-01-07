# 通过注释提高Python代码的可读性

> 原文：[https://towardsdatascience.com/enhancing-readability-of-python-code-via-annotations-09ce4c9b3729?source=collection_archive---------5-----------------------#2024-04-12](https://towardsdatascience.com/enhancing-readability-of-python-code-via-annotations-09ce4c9b3729?source=collection_archive---------5-----------------------#2024-04-12)

## PYTHON 编程

## 注释是一个强大的开发工具。阅读本文以了解如何以及在哪里使用它们。

[](https://medium.com/@nyggus?source=post_page---byline--09ce4c9b3729--------------------------------)[![Marcin Kozak](../Images/d7faf62e48ed81dab5d8ad92819fff54.png)](https://medium.com/@nyggus?source=post_page---byline--09ce4c9b3729--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--09ce4c9b3729--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--09ce4c9b3729--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--09ce4c9b3729--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--09ce4c9b3729--------------------------------) ·阅读时间：21分钟·2024年4月12日

--

![](../Images/5161c0a1d90b47f723881d767e785f08.png)

代码注释就像用标记笔做高亮。照片由[Mitchell Luo](https://unsplash.com/@mitchel3uo?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

代码清晰既是一种美德，也是一种必需。如果你写出清晰易读的代码，其他开发者能够理解它，用户能够明白如何使用它，甚至未来的你也会感谢它，因为随着时间的推移，我们大多数人都会忘记我们实现的代码的细节。随着项目和代码库规模的不断扩大，代码清晰性变得越来越重要。

在编程语言中，Python提供了非常易读的代码。或者更确切地说，Python*可以*提供非常易读的代码——但你需要知道如何使其易读。我甚至可以说，Python之所以如此流行，有几个原因，其中一个重要原因就是代码的可读性。因此，写出好的Python代码是我们的责任。为了做到这一点，我们需要工具。

> 代码清晰既是一种美德，也是一种必需。

有许多工具可以提高Python代码质量。首先，我们需要编写符合良好Python代码标准的代码，这些标准由PEP 8提供：
