# Python Set 确实比 List 快：是真的吗？

> 原文：[https://towardsdatascience.com/python-set-is-way-faster-than-list-true-or-false-042c6f8975cd?source=collection_archive---------2-----------------------#2024-07-29](https://towardsdatascience.com/python-set-is-way-faster-than-list-true-or-false-042c6f8975cd?source=collection_archive---------2-----------------------#2024-07-29)

![](../Images/ff53ac41df268fa6ffa5e4e926b0ec7d.png)

作者在 Canva 中创建

## 综合性能比较与数据结构讨论

[](https://christophertao.medium.com/?source=post_page---byline--042c6f8975cd--------------------------------)[![Christopher Tao](../Images/bea1e3c81cc62eb28bdba9275d6b326f.png)](https://christophertao.medium.com/?source=post_page---byline--042c6f8975cd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--042c6f8975cd--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--042c6f8975cd--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--042c6f8975cd--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--042c6f8975cd--------------------------------) ·阅读时间 7 分钟·2024年7月29日

--

几周前，我写了另一篇文章，解释了一些流行“Python 技巧”背后的机制和逻辑。其中之一是，当可能时，使用 Python Set 而不是 List。

[](/many-articles-tell-you-python-tricks-but-few-tell-you-why-d4953d24e80b?source=post_page-----042c6f8975cd--------------------------------) [## 许多文章告诉你 Python 技巧，但很少告诉你背后的原因

### 三个常见的 Python 技巧可以让你的程序更快，我将解释它们的机制

[towardsdatascience.com](/many-articles-tell-you-python-tricks-but-few-tell-you-why-d4953d24e80b?source=post_page-----042c6f8975cd--------------------------------)

这篇文章发布后，许多读者问我或争论 Python Set 并不总是更快。这完全正确。因此，我决定写这篇文章，深入探讨 Python List 和 Set 的数据结构。

在本文中，我将首先使用实际的代码，在不同的场景下比较 Python List 和 Set 的性能。然后，我将介绍它们使用的数据结构——动态数组和哈希表。基于这些数据结构的特性，我将解释为什么 Python List 或 Set 在某些场景下具有更好的性能。

# 1\. 性能
