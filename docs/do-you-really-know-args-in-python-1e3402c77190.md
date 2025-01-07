# 你真的了解 Python 中的 *args 吗？

> 原文：[`towardsdatascience.com/do-you-really-know-args-in-python-1e3402c77190?source=collection_archive---------7-----------------------#2024-01-29`](https://towardsdatascience.com/do-you-really-know-args-in-python-1e3402c77190?source=collection_archive---------7-----------------------#2024-01-29)

![](img/faa4d592c0198ae3cdc77079dec57809.png)

图片来源：[Miguel Á. Padriñán](https://pixabay.com/users/padrinan-1694659/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1738220) 来自 [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1738220)

## *args 的全面指南与实用示例

[](https://christophertao.medium.com/?source=post_page---byline--1e3402c77190--------------------------------)![Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--1e3402c77190--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1e3402c77190--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1e3402c77190--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--1e3402c77190--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1e3402c77190--------------------------------) ·阅读时间 9 分钟·2024 年 1 月 29 日

--

作为 Python 中最独特的语法之一，`*args` 在编程过程中能为我们提供极大的灵活性和便利。我会说，它们体现了所谓的“Pythonic”风格和 Python 的禅意。

然而，我发现学习者往往难以理解它们。在本文中，我将尽我所能解释这个 Python 中标志性的概念，并根据我的知识提供实际应用案例。希望能帮助你更好地理解它。

# 1\. “*args” 到底是什么？

![](img/ca98a65a1a49ccc0f0a6f6b486e740c9.png)

图片来源：[Thomas](https://pixabay.com/users/didgeman-153208/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3851974) 来自 [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=3851974)

`*args` 代表“参数”。它允许我们将任意数量的 **位置参数**（稍后解释）传递给一个函数。在函数内部，我们可以将所有位置参数存储为元组。因此，我们可以在函数中对这些参数元组进行任意操作。

这是一个简单的 `*args` 示例。

```py
def add_up(*numbers):
    result = 0
    for num in numbers:
        result += num
    return result

print(add_up(1, 2, 3))
```
