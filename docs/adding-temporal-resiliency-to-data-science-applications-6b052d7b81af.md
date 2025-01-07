# 向数据科学应用添加时间弹性

> 原文：[https://towardsdatascience.com/adding-temporal-resiliency-to-data-science-applications-6b052d7b81af?source=collection_archive---------5-----------------------#2024-03-26](https://towardsdatascience.com/adding-temporal-resiliency-to-data-science-applications-6b052d7b81af?source=collection_archive---------5-----------------------#2024-03-26)

## 我们的应用能否在不花费任何冗余资源的情况下，获得冗余所带来的可靠性好处？

[](https://medium.com/@rohitpandey576?source=post_page---byline--6b052d7b81af--------------------------------)[![Rohit Pandey](../Images/af817d8f68f2984058f0afb8fd7ecbe9.png)](https://medium.com/@rohitpandey576?source=post_page---byline--6b052d7b81af--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6b052d7b81af--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6b052d7b81af--------------------------------) [Rohit Pandey](https://medium.com/@rohitpandey576?source=post_page---byline--6b052d7b81af--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6b052d7b81af--------------------------------) ·13 分钟阅读·2024年3月26日

--

![](../Images/0e6319aa63a42a694776ce5c00399319.png)

图片来自midjourney

现代应用几乎完全将其状态存储在数据库中，并从数据库中读取执行任务所需的状态。我们将关注如何为从这些数据库读取和写入的过程添加弹性，使其变得高度可靠。

实现这一目标的明显方法是提高组成数据库的硬件和软件的质量，使我们的读写操作从不失败。但这会变成一个递减效益的法则：一旦我们已经达到较高的可用性，投入更多资金所带来的变化仅是微不足道的。添加冗余来实现高可用性很快就变成了一个更好的策略。

那么，通过向架构中添加冗余来实现高可靠性到底是什么样的呢？我们通过花费更多资金在冗余系统上来消除单点故障。例如，保持数据的冗余副本，以便如果一个副本损坏或丢失，其他副本可以用来修复。另一个例子是拥有冗余数据库，当主数据库不可用时，可以从中读取和写入数据。我们将这些解决方案称为……
