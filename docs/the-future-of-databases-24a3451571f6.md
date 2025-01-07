# 数据库的未来 — 图关系型数据库

> 原文：[https://towardsdatascience.com/the-future-of-databases-24a3451571f6?source=collection_archive---------11-----------------------#2024-05-22](https://towardsdatascience.com/the-future-of-databases-24a3451571f6?source=collection_archive---------11-----------------------#2024-05-22)

## 多模型以及ISO GQL的标准即将发布...

[](https://victormorgante.medium.com/?source=post_page---byline--24a3451571f6--------------------------------)[![Victor Morgante](../Images/46b797426e0d0840c0d0f6dd76f46ae5.png)](https://victormorgante.medium.com/?source=post_page---byline--24a3451571f6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--24a3451571f6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--24a3451571f6--------------------------------) [Victor Morgante](https://victormorgante.medium.com/?source=post_page---byline--24a3451571f6--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--24a3451571f6--------------------------------) ·6分钟阅读·2024年5月22日

--

![](../Images/a79edf83a5ff5692b6d92b1123386173.png)

版权免费图片，Dall-E 3 和 Victor Morgante

三年半前，当我写下《[所有数据库都是多模型的](https://victormorgante.medium.com/all-databases-are-multi-model-169ce22e4593)》时，我其实是在瞎猜。我刚刚开始创办FactEngine，并且有一个愿景，那就是每个数据库都可以看作是图数据库和关系数据库的结合体——一个多模型数据库。当时已经有一些多模型数据库存在，大型公司如SQL Server也开始涉足多模型世界。

快进三年，ISO（国际标准化组织）刚刚发布了一项新标准，[ISO/IEC 39075:2024](https://www.iso.org/standard/76120.html) —— 信息技术 —— 数据库语言 —— GQL……其中GQL代表图查询语言，标准设计旨在同时作为现有关系数据库的覆盖层，也作为未来图数据库的标准。多模型已成为主流。

读者们对这背后的数据库理论/数据科学和标准本身同样感兴趣。

经常阅读我博客的朋友们应该已经看过这张图片几次了，因此请耐心一下，我们将与更广泛的读者群体分享。这是一张展示关系数据库向属性图架构/图数据库结构演化的图片，显示了二者之间的同态关系：
