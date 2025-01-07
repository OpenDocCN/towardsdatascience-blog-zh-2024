# Pandas 列：括号索引（df[‘x’]）与点语法（df.x）

> 原文：[`towardsdatascience.com/pandas-columns-bracket-indexing-df-x-versus-dot-syntax-df-x-aedd38f4a350?source=collection_archive---------1-----------------------#2024-03-01`](https://towardsdatascience.com/pandas-columns-bracket-indexing-df-x-versus-dot-syntax-df-x-aedd38f4a350?source=collection_archive---------1-----------------------#2024-03-01)

## PANDAS 数据科学应用

## 使用哪种方式有区别吗？也许某种方式比另一种更快？

[](https://medium.com/@nyggus?source=post_page---byline--aedd38f4a350--------------------------------)![Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--aedd38f4a350--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--aedd38f4a350--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--aedd38f4a350--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--aedd38f4a350--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--aedd38f4a350--------------------------------) ·阅读时间：5 分钟·2024 年 3 月 1 日

--

![](img/33495307aa2cf1f1805eaad074017b2c.png)

点语法在 Python 中非常流行，在 Pandas 中也广泛使用。图片来源：[Alejandro Barba](https://unsplash.com/@albrb?utm_source=medium&utm_medium=referral) 提供的 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

使用 Pandas 时，大多数数据科学家会选择 `df['x']` 或 `df["x"]`——其实这两个用法没有太大区别，只要你选定其中一个并坚持使用即可。你可以在这里查看更多相关内容：

[](https://medium.com/pythoniq/so-single-or-double-quotes-in-python-47c2e7425f32?source=post_page-----aedd38f4a350--------------------------------) [## 所以，Python 中使用单引号（‘）还是双引号（“）？

### 很多人认为你应该在 Python 中偏好使用单引号而不是双引号。你真的应该这么做吗？

medium.com](https://medium.com/pythoniq/so-single-or-double-quotes-in-python-47c2e7425f32?source=post_page-----aedd38f4a350--------------------------------)

因此，从现在开始，无论我写的是 `df["x"]`，它都等同于 `df['x']`。不过，还有另一种选择。你也可以使用 `df.x`。虽然这种方法不太常见，但它能提高代码的可读性，前提是列名是一个[有效的 Python 标识符](https://docs.python.org/3/reference/lexical_analysis.html#identifiers)。¹

选择哪种语法真的有区别吗？本文旨在从两个最重要的角度讨论这个问题：可读性和性能。

# 优缺点

这两种方法——`df["x"]` 和 `df.x`——是从数据框（此处为 `df`）中访问列（此处为 `"x"`）的常见方式。在数据科学领域，大多数…
