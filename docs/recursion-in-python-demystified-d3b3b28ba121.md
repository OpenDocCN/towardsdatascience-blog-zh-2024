# 递归在 Python 中的解密

> 原文：[`towardsdatascience.com/recursion-in-python-demystified-d3b3b28ba121?source=collection_archive---------7-----------------------#2024-02-12`](https://towardsdatascience.com/recursion-in-python-demystified-d3b3b28ba121?source=collection_archive---------7-----------------------#2024-02-12)

## PYTHON 编程

## 文章展示了 Python 中平面和嵌套递归模式的简单示例。

[](https://medium.com/@nyggus?source=post_page---byline--d3b3b28ba121--------------------------------)![Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--d3b3b28ba121--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d3b3b28ba121--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d3b3b28ba121--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--d3b3b28ba121--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d3b3b28ba121--------------------------------) ·15 分钟阅读·2024 年 2 月 12 日

--

![](img/b790fa6fa7dd25e7ee4129cb1874bb59.png)

照片由[Nareeta Martin](https://unsplash.com/@splashabout?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

简单来说，递归函数是一个调用自身的函数。

这听起来可能很简单，但如果你尝试深入了解，你很可能会发现关于递归的解释远不如表面上看起来那么简单。这是因为上面对递归的简单定义并没有深入细节，要完全理解递归的工作原理，你需要知道比这句话所传达的更多内容：

> 递归函数是一个调用自身的函数。

例如，查看[维基百科](https://en.wikipedia.org/wiki/Recursion_(computer_science))。里面充满了技术术语，解释远非简单，特别是对于没有 IT 或数学相关背景的初学者来说。仅凭这样的解释，初学者很难实现自己的递归函数。

尽管一些递归函数乍一看可能非常简单，但尝试实现你的第一个递归逻辑可能会非常困难。这可能是个艰巨的任务，因为你需要改变自己对问题的思考方式。

大多数数据科学家将编程任务视为一系列更小的步骤，这些步骤最终会……
