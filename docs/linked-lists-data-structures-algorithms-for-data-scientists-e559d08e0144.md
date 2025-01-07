# 链表 — 数据结构与算法（面向数据科学家）

> 原文：[`towardsdatascience.com/linked-lists-data-structures-algorithms-for-data-scientists-e559d08e0144?source=collection_archive---------4-----------------------#2024-10-21`](https://towardsdatascience.com/linked-lists-data-structures-algorithms-for-data-scientists-e559d08e0144?source=collection_archive---------4-----------------------#2024-10-21)

## 链表和队列是如何在幕后工作的

[](https://medium.com/@egorhowell?source=post_page---byline--e559d08e0144--------------------------------)![Egor Howell](https://medium.com/@egorhowell?source=post_page---byline--e559d08e0144--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e559d08e0144--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e559d08e0144--------------------------------) [Egor Howell](https://medium.com/@egorhowell?source=post_page---byline--e559d08e0144--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e559d08e0144--------------------------------) ·6 分钟阅读·2024 年 10 月 21 日

--

![](img/357d6a3185097846a4031d6e556a2459.png)

图片由 [Luca Bravo](https://unsplash.com/@lucabravo?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在上一篇关于数组的文章之后，我想讨论链表数据结构及其一些操作。

[](/arrays-data-structures-algorithms-for-data-scientists-b2e39e25136d?source=post_page-----e559d08e0144--------------------------------) ## 数组 — 数据结构与算法（面向数据科学家）

### 动态和静态数组是如何在幕后工作的

towardsdatascience.com

作为数据科学家，尽管我们不一定需要了解数据结构和算法的每个细节，但这仍然是任何技术专业人员非常实用的技能集。

这就是为什么我会创建一系列文章，讨论核心数据结构以及它们如何在幕后工作。

# 什么是链表？

[链表](https://en.wikipedia.org/wiki/Linked_list)是一种数据结构，其中每个元素（称为节点）都按顺序存储/工作。然而，链表不像数组那样在内存中连续存储。

每个节点有两部分内容：

+   **数据** — 该节点所包含的值

+   **引用** — 节点包含对序列中下一个节点的引用。
