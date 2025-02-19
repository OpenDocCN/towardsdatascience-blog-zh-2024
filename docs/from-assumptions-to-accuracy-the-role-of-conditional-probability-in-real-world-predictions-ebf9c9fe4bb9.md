# 从假设到准确性：条件概率在现实世界预测中的作用

> 原文：[`towardsdatascience.com/from-assumptions-to-accuracy-the-role-of-conditional-probability-in-real-world-predictions-ebf9c9fe4bb9?source=collection_archive---------2-----------------------#2024-05-25`](https://towardsdatascience.com/from-assumptions-to-accuracy-the-role-of-conditional-probability-in-real-world-predictions-ebf9c9fe4bb9?source=collection_archive---------2-----------------------#2024-05-25)

## ***条件概率优于概率；前提是你有相关的信息***

[](https://medium.com/@atisharajpurohit?source=post_page---byline--ebf9c9fe4bb9--------------------------------)![Atisha Rajpurohit](https://medium.com/@atisharajpurohit?source=post_page---byline--ebf9c9fe4bb9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ebf9c9fe4bb9--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ebf9c9fe4bb9--------------------------------) [Atisha Rajpurohit](https://medium.com/@atisharajpurohit?source=post_page---byline--ebf9c9fe4bb9--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ebf9c9fe4bb9--------------------------------) ·5 分钟阅读·2024 年 5 月 25 日

--

![](img/1f6efb043aed0500e12b0b8939bd0851.png)

图片来源：[Unsplash](https://unsplash.com/s/photos/dice)

**引言**

任何学过概率的人都听过这个长期以来确立的概率定义：“*概率可以定义为有利结果的数量除以所有结果的总数*。”我仍然能听到我四年级的老师一遍又一遍地重复这句话！

虽然这个定义是**正确的**，但我常常会想，这个定义在现实世界中的**准确性**如何？当我们对有利结果有**更多信息**时，它的准确性又如何？更明确地说，当我们对有利结果有更多的“**条件**”时，它的准确性如何。

引入“**条件**”就像是通过多种条件，将你原本的有利结果的数量划分成不同的切片，给你一个更真实代表有利结果数量的切片。下面的图片试图简要地描绘这个概念。

![](img/1512c53f282abead9a1af6e0b39ffde4.png)

图片由作者创作

*还有什么比代表许多在美国学习并寻找工作的国际数据科学学生心中所想的更好的方式呢！最初的可用工作数量显示在最左边。*

***第一个条件：*** *引入“****工作经验时长****”的第一个条件*，细化了新加入者可获得工作的数量。

***第二个条件：*** *此外，加入* ***第二个条件*** *“****国籍/公民身份****”的因素，进一步精细化了这一部分。*

***第 3 个条件*** *：最右侧图表中的小深蓝色部分代表了最准确的可用工作数量（即有利结果的数量）。*

在深入探讨为什么条件概率可能比普通概率更有用之前，让我们快速回顾一下定义。

**2. 概率和条件概率的定义**

**概率：**

P(A) = A 的有利结果数量 / 总结果数量

**条件概率：**

现在考虑两个事件 A 和 B。条件概率的基础是“事件给定另一个事件”。在这种情况下，当我们说**A 给定 B**时，意味着事件 A 发生**在**事件 B 已经发生的情况下。所以就是将“**B 的条件，附加到 A 上**”。

P(A|B) = P(A 与 B 的交集) / P(B)，其中

P(A 与 B 的交集)* 被定义为事件 A 和事件 B 同时发生的概率。

**假设 (A 与 B 的交集) 和 (A’ 与 B 的交集) 是互斥的。因此 (A 与 B 的交集) 并集 (A’ 与 B 的交集) = B。**

在定义这些稍显混淆的定义后，我将讲解为什么我认为条件概率实际上更好。

**3. 示例 — 这篇文章的灵感来源**

说实话，写这篇文章的灵感来自于前几天我看的一部宝莱坞电影，其中有一个场景，两个老朋友在讨论他们相遇的概率！

让我介绍一些关于这个场景的更多信息：

+   **第 1 个朋友**：**警察**，来自孟买市，前往卡林蓬（Kalimpong）——一个小镇，处理一起案件。

+   **第 2 个朋友**：**数学教授**，是卡林蓬镇的居民。

这两个朋友彼此认识，因为他们曾经在**同一所大学**学习。

+   目前，两个朋友在一家咖啡馆碰面，教授每天都会去那里。

介绍完这些信息后，让我们回到问题：他们在卡林蓬相遇的概率是多少。

*警察：“兄弟，几率有多大！”*

*数学教授：“1/95,675。”*

*警察：“错了！你没有算上我。”*

*数学教授：“我知道了。目前人口是 95,674。”*

嗯…让我们分解一下这个逻辑：

**初步概率计算：**

+   数学教授计算出遇到他朋友——警察的概率是 1/95,675。

+   这个假设前提是卡林蓬的 95,674 名居民与警察碰面的概率与遇到教授的概率相同。

**为什么这个计算不准确：**

+   这个计算假设遇到警察与遇到卡林蓬的任何其他居民是一样的！

**引入条件概率：**

让我们考虑一些特定的情景。

**I. 相关信息：**

+   警察是来自孟买的居民，他前往卡林蓬。

+   这位数学教授每天都会去这家咖啡馆。

+   那位警察刚好在这次去同一家咖啡馆。

**II. 条件事件：**

**事件 A**：教授和警察在卡林蓬见面。

**事件 B**：这位警察从孟买旅行到卡林蓬。

两位朋友见面的概率：

![](img/e25a3a0714fcff2ef5bb6260efa63248.png)

1. 警察从孟买到卡林蓬旅行的概率，取决于以下因素：

+   他多久出差一次？

+   他有多频繁被分配去处理小镇的案件？

+   假设这个概率是 0.1%。

2. 两位朋友见面的概率取决于以下因素：

+   他们两个人有多频繁去这家咖啡馆？

+   这家咖啡馆有多受欢迎？

+   教授定期去这家咖啡馆。

+   假设这个概率是 1%。

**最终计算：**

![](img/17ebe385d5356ef5c636d4f1879617fb.png)

+   在警察已经在卡林蓬的情况下，两位朋友在卡林蓬见面的概率是 0.001%。

+   这是对这个概念的简化表示，但我想表达的是，要始终寻找更多相关的信息来细化你的概率。

**结论**

概率既简单又复杂！然而，随着我们获得更多的信息，我们总能做出更精确的推理。在现实世界中，始终尝试寻找额外的信息，看看它如何帮助你添加条件，从而使你的概率更为准确。

感谢阅读，希望这篇文章对你有所帮助！
