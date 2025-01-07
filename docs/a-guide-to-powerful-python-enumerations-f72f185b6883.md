# 强大的 Python 枚举指南

> 原文：[`towardsdatascience.com/a-guide-to-powerful-python-enumerations-f72f185b6883?source=collection_archive---------2-----------------------#2024-05-20`](https://towardsdatascience.com/a-guide-to-powerful-python-enumerations-f72f185b6883?source=collection_archive---------2-----------------------#2024-05-20)

## PYTHON 编程

## 通过简单、易读且高效的枚举来改进数据科学中的 Python 代码

[](https://medium.com/@nyggus?source=post_page---byline--f72f185b6883--------------------------------)![Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--f72f185b6883--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f72f185b6883--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f72f185b6883--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--f72f185b6883--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f72f185b6883--------------------------------) ·阅读时间 23 分钟·2024 年 5 月 20 日

--

![](img/7a7c2ad5c5e03ba5cf64f32629c38a70.png)

Python 枚举提供了有用的数据类型。图片来源：[Waldemar](https://unsplash.com/@waldemarbrandt67w?utm_source=medium&utm_medium=referral) via [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

枚举类型在多种编程语言中都有应用，例如 C、C#、C++、Java、Go、Rust——当然也包括 Python。不知道出于什么原因，Python 枚举在数据科学领域常常被低估和少用。我自己虽然相对频繁地使用它们，并且也看到过它们的使用——但绝对是远远不够的。我希望这篇文章能够在改变这一情况上迈出小小的一步。

Python 枚举提供了一个非常强大的工具，不容忽视。如果一个人不知道枚举是如何工作的，可能会认为它过于复杂——这也是为什么最好先学习它们的工作原理，再尝试使用它们。我希望在阅读完这篇文章后，你能同意我的看法：枚举不仅不会使代码复杂，反而能够让代码变得更加简洁、短小、减少错误——甚至提升性能。

我们将通过数据科学中的一个具体示例来讨论枚举类型。为此，我们将定义并比较四个类，其中两个基于字符串和 `typing.Literal` 类型的组合，另外两个则基于 `enum.Enum`，这是构成标准库的类…
