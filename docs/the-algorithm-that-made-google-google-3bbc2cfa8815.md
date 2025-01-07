# 让Google变成Google的算法

> 原文：[https://towardsdatascience.com/the-algorithm-that-made-google-google-3bbc2cfa8815?source=collection_archive---------1-----------------------#2024-12-18](https://towardsdatascience.com/the-algorithm-that-made-google-google-3bbc2cfa8815?source=collection_archive---------1-----------------------#2024-12-18)

## PageRank如何改变了我们搜索互联网的方式，以及它为何在LLMs与图形RAG中仍然发挥着重要作用。

[](https://medium.com/@cristianleo120?source=post_page---byline--3bbc2cfa8815--------------------------------)[![Cristian Leo](../Images/99074292e7dfda50cf50a790b8deda79.png)](https://medium.com/@cristianleo120?source=post_page---byline--3bbc2cfa8815--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3bbc2cfa8815--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3bbc2cfa8815--------------------------------) [Cristian Leo](https://medium.com/@cristianleo120?source=post_page---byline--3bbc2cfa8815--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3bbc2cfa8815--------------------------------) ·阅读时间：16分钟·2024年12月18日

--

![](../Images/b9c5d8296fadf79dceaa79c41cdd3643.png)

由DALL-E生成的图像

在1990年代末，两位斯坦福大学的研究生，拉里·佩奇和谢尔盖·布林，在他们的博士研究中遇到了一个有趣的想法。

拉里特别着迷于网页之间如何相互链接的方式。他将互联网看作一个庞大的引用网络，就像学术论文互相引用一样。这激发了一个想法：**如果一个网页的重要性可以通过其他页面链接到它的数量来衡量，会怎样？** 但不仅如此——**如果这些链接页面的重要性也很重要呢？**

对这个想法产生了兴趣后，拉里开始构建一个后来被命名为“PageRank”（这是他姓氏的巧妙变换）的算法。这个算法将每个指向网页的链接视为对网页的信任投票，但有一个 twist ——来自更重要页面的投票具有更高的权重。

对拉里的这个概念感兴趣的谢尔盖·布林加入了他。两人一起从宿舍开始，构建了一个将使用这一排名系统的搜索引擎。最初，他们将他们的项目命名为“BackRub”，因为它分析了反向链接。
