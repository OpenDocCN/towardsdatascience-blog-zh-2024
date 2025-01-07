# 我如何评估我的 Python 代码的内存消耗

> 原文：[`towardsdatascience.com/how-i-assess-the-memory-consumption-of-my-python-code-7e890c0b2709?source=collection_archive---------8-----------------------#2024-05-28`](https://towardsdatascience.com/how-i-assess-the-memory-consumption-of-my-python-code-7e890c0b2709?source=collection_archive---------8-----------------------#2024-05-28)

![](img/18343c1969b363a3d736644a35cb57cc.png)

由作者在 Canva 中创建

## 测量变量或函数内存消耗的不同方法

[](https://christophertao.medium.com/?source=post_page---byline--7e890c0b2709--------------------------------)![Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--7e890c0b2709--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7e890c0b2709--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7e890c0b2709--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--7e890c0b2709--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7e890c0b2709--------------------------------) ·6 分钟阅读·2024 年 5 月 28 日

--

在编写代码时，我们过去常常关注性能。如果我们搜索“python performance”，很容易找到成千上万篇文章。然而，我很少看到有文章告诉我们如何优化代码的内存消耗。

当然，性能在编程中至关重要，但内存消耗将决定我们需要在硬件上花费多少资金。试想我们要在一个大数据集上训练一个模型。我们可能需要一台 64GB 内存的虚拟机来完成这个任务。然而，如果我们能够优化我们的代码，32GB 内存可能就能实现类似的运行时间。那样的话，我们可能会为硬件资源节省大量成本。

在我优化代码以减少内存消耗之前，我们需要一些有效的方法来衡量它。此外，通过了解衡量内存消耗的方法，我们将能够量化并基准测试它。因此，在本文中，我将介绍我常用的一些内存测量方法。

# 1\. Python 内置的“获取大小”方法
