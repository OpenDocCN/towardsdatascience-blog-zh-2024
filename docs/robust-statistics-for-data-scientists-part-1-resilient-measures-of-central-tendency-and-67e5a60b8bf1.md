# 数据科学家的鲁棒统计学 第一部分：中央趋势与离散度的韧性度量

> 原文：[`towardsdatascience.com/robust-statistics-for-data-scientists-part-1-resilient-measures-of-central-tendency-and-67e5a60b8bf1?source=collection_archive---------14-----------------------#2024-01-30`](https://towardsdatascience.com/robust-statistics-for-data-scientists-part-1-resilient-measures-of-central-tendency-and-67e5a60b8bf1?source=collection_archive---------14-----------------------#2024-01-30)

## 构建基础：理解和应用数据分析中的鲁棒性度量

[](https://medium.com/@le_Tomassini?source=post_page---byline--67e5a60b8bf1--------------------------------)![Alessandro Tomassini](https://medium.com/@le_Tomassini?source=post_page---byline--67e5a60b8bf1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--67e5a60b8bf1--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--67e5a60b8bf1--------------------------------) [Alessandro Tomassini](https://medium.com/@le_Tomassini?source=post_page---byline--67e5a60b8bf1--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--67e5a60b8bf1--------------------------------) ·阅读时间 10 分钟·2024 年 1 月 30 日

--

![](img/768cfc2134084b1fe15efc68c7cc3270.png)

图像由 DALL-E 生成

统计学在数据科学中的角色至关重要，它架起了原始数据与可操作见解之间的桥梁。然而，并非所有统计方法都平等，尤其是在面对（杂乱）现实世界数据的严峻挑战时。这将我们引向鲁棒统计学的目的，它是一个旨在抵抗数据异常的子领域，这些异常常常使传统统计方法偏离正确的方向。

# 经典统计学与鲁棒统计学：一次必要的转变

尽管经典统计学为我们提供了很多帮助，但它们对异常值和极端值的敏感性可能会导致误导性结论。此时，鲁棒统计学应运而生，旨在在更广泛的条件下提供更可靠的结果。这种方法并不是在没有考虑的情况下丢弃异常值，而是开发出对异常值不太敏感的方法。

鲁棒统计学基于韧性原理。它是构建那些在假设出现小偏差时仍能保持不变或影响最小的统计方法，而这些假设是传统方法所依赖的。这…
