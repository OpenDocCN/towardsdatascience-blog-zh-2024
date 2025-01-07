# 如何使用 Python 布隆过滤器仅用 77MB 存储和查询 1 亿项数据

> 原文：[`towardsdatascience.com/how-to-store-and-query-100-million-items-using-just-77mb-with-python-bloom-filters-6b3e8549f032?source=collection_archive---------2-----------------------#2024-02-08`](https://towardsdatascience.com/how-to-store-and-query-100-million-items-using-just-77mb-with-python-bloom-filters-6b3e8549f032?source=collection_archive---------2-----------------------#2024-02-08)

## 使用这个必备的数据结构，在 Python 中执行极速且内存高效的成员资格检查

[](https://mikehuls.medium.com/?source=post_page---byline--6b3e8549f032--------------------------------)![Mike Huls](https://mikehuls.medium.com/?source=post_page---byline--6b3e8549f032--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6b3e8549f032--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6b3e8549f032--------------------------------) [Mike Huls](https://mikehuls.medium.com/?source=post_page---byline--6b3e8549f032--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6b3e8549f032--------------------------------) ·阅读时长 11 分钟·2024 年 2 月 8 日

--

![](img/97200918356e1b6e481565d52747e46a.png)

带有视图的编程（图片来源：ChatGPT）

布隆过滤器是一种超级快速、内存高效的数据结构，具有多种应用场景。布隆过滤器回答一个简单的问题：一个集合是否包含某个给定的值？一个好的布隆过滤器可以包含 1 亿项数据，使用仅 77MB 的内存，并且依然非常快速。它通过概率方式实现了这一惊人的效率：当你问它是否包含某个项时，它有两种回应方式：**绝对不包含**或**可能包含**。

> 布隆过滤器可以告诉你**肯定**某个项**不**是集合的成员，或者它**可能是**集合的成员

在本文中，我们将了解布隆过滤器的工作原理，如何实现一个布隆过滤器，并且将讨论一些实际的使用案例。最后，你将拥有一个新的工具，可以显著优化你的脚本！让我们开始编码吧！

# 在我们开始之前……

本文探讨了布隆过滤器的机制，并提供了一个基本的 Python 实现，展示其内部工作原理，共分 6 步：

1.  什么时候使用布隆过滤器？特点和应用场景
