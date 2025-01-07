# 很多文章告诉你 Python 技巧，但很少有人告诉你为什么。

> 原文：[`towardsdatascience.com/many-articles-tell-you-python-tricks-but-few-tell-you-why-d4953d24e80b?source=collection_archive---------1-----------------------#2024-03-24`](https://towardsdatascience.com/many-articles-tell-you-python-tricks-but-few-tell-you-why-d4953d24e80b?source=collection_archive---------1-----------------------#2024-03-24)

![](img/a06d882cd05e9a6849f99503ce805412.png)

图片来自 [Annette](https://pixabay.com/users/anncapictures-1564471/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2034146) 来自 [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2034146)

## 三个常见的 Python 技巧可以让你的程序运行得更快，我将解释它们背后的原理。

[](https://christophertao.medium.com/?source=post_page---byline--d4953d24e80b--------------------------------)![Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--d4953d24e80b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d4953d24e80b--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d4953d24e80b--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--d4953d24e80b--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d4953d24e80b--------------------------------) ·阅读时长 8 分钟·2024 年 3 月 24 日

--

我刚刚做了一个简单的搜索，很容易找到许多试图告诉我们各种 Python 技巧的文章。它们要么是更“Pythonic”，要么是让我们的程序运行得更快。这些文章没有错，因为大多数技巧都非常有用。事实上，我自己也写过很多这样的文章。

然而，这类文章常常受到批评，因为没有一种技巧适用于所有场景。这也是事实。在我看来，更重要的是理解这些技巧为何存在，这样我们就能知道何时使用它们，何时不使用。

在这篇文章中，我将挑选出其中的三种技巧，并提供它们背后机制的详细解释。

# 1\. 更快地连接字符串

![](img/b268dca4f7cf786e44cbed48ac48659c.png)

图片来自 [浩一 萩原](https://pixabay.com/users/u_dg9pheol-9978493/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3644538) 来自 [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3644538)

你通常是如何连接字符串的？

例如，假设我们有一个需要连接在一起的字符串列表。

```py
strs = ['Life', 'is', 'short,'…
```
