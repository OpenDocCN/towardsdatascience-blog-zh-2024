# 系统设计：布隆过滤器

> 原文：[https://towardsdatascience.com/system-design-bloom-filter-a2e19dcd4810?source=collection_archive---------3-----------------------#2024-03-24](https://towardsdatascience.com/system-design-bloom-filter-a2e19dcd4810?source=collection_archive---------3-----------------------#2024-03-24)

## 智能地将哈希表转化为概率数据结构，牺牲精度以换取更大的内存空间

[](https://medium.com/@slavahead?source=post_page---byline--a2e19dcd4810--------------------------------)[![Vyacheslav Efimov](../Images/441e600862b2b93564c6cd81abb0092d.png)](https://medium.com/@slavahead?source=post_page---byline--a2e19dcd4810--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a2e19dcd4810--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a2e19dcd4810--------------------------------) [Vyacheslav Efimov](https://medium.com/@slavahead?source=post_page---byline--a2e19dcd4810--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a2e19dcd4810--------------------------------) ·阅读时间6分钟·2024年3月24日

--

![](../Images/2e1fcf1dc1e730a621e06a24289dd872.png)

# 介绍

**哈希表**是最广为人知和使用的数据结构之一。通过合理选择哈希函数，哈希表能够在常数时间内为插入、查找和删除操作提供最佳性能。

哈希表的主要缺点是潜在的冲突。为了解决这个问题，标准方法之一是增加哈希表的大小。虽然这种方法在大多数情况下效果良好，但有时我们仍然会受到使用大量内存空间的限制。

需要回顾的是，哈希表总是能够为任何查询提供正确的响应。虽然它可能会发生冲突并且有时会变慢，但它**始终****保证100%正确的响应**。事实证明，在某些系统中，我们并不总是需要收到正确的查询信息。对准确性的这种小幅下降可以用来专注于改善系统的其他方面。

在本文中，我们将探索一种创新的数据结构——**布隆过滤器**。简而言之，它是标准哈希表的一个修改版本，通过牺牲少量的准确性来换取内存空间的优化。
