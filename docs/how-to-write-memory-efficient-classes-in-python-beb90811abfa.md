# 如何编写内存高效的 Python 类

> 原文：[https://towardsdatascience.com/how-to-write-memory-efficient-classes-in-python-beb90811abfa?source=collection_archive---------0-----------------------#2024-01-13](https://towardsdatascience.com/how-to-write-memory-efficient-classes-in-python-beb90811abfa?source=collection_archive---------0-----------------------#2024-01-13)

![](../Images/6d3fa23de320d6f4dd772d2fc675018d.png)

图片来源：[Christian Dubovan](https://unsplash.com/@cdubo?utm_source=medium&utm_medium=referral) 摄于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 防止你的数据项目内存溢出的三种技巧

[](https://medium.com/@siavashyasini?source=post_page---byline--beb90811abfa--------------------------------)[![Siavash Yasini](../Images/55220a8a9397ba51dcc381828735f4a2.png)](https://medium.com/@siavashyasini?source=post_page---byline--beb90811abfa--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--beb90811abfa--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--beb90811abfa--------------------------------) [Siavash Yasini](https://medium.com/@siavashyasini?source=post_page---byline--beb90811abfa--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--beb90811abfa--------------------------------) ·阅读时间：7分钟·2024年1月13日

--

几年前，我写过一篇关于如何编写内存高效的 Python 循环的博客文章，并且它获得了相当好的反响。积极的反馈鼓励我写了第二部分，深入探讨了更多的内存优化方法。

在编写 Python 代码时，循环并不是我们唯一需要关注内存使用的地方。在与数据相关的项目和面向对象的代码开发中，确保我们的类也具备内存效率非常重要。我们常常花费大量时间设计和编写复杂精细的类，结果却发现它们在测试或生产中由于需要承载大量数据而表现不佳。

通过遵循本文中讨论的技术和方法，你可以创建优化内存使用、提升整体性能的类。本文将探讨三种技巧和推荐的方法，帮助你编写内存高效的 Python 类。

# 1. 使用 __slots__

使用 Python 的 `__slots__` 特性，你可以显式地定义一个类可以拥有的属性。这通常有助于通过避免创建额外的字典来优化我们类的内存使用……
