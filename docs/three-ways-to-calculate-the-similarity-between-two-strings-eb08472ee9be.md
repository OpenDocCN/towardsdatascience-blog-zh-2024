# 计算两个字符串相似性的三种方法

> 原文：[https://towardsdatascience.com/three-ways-to-calculate-the-similarity-between-two-strings-eb08472ee9be?source=collection_archive---------5-----------------------#2024-02-15](https://towardsdatascience.com/three-ways-to-calculate-the-similarity-between-two-strings-eb08472ee9be?source=collection_archive---------5-----------------------#2024-02-15)

## textPython，文本分析

## 使用 jellyfish 库的 Python 教程来计算两个字符串之间的相似性

[](https://alod83.medium.com/?source=post_page---byline--eb08472ee9be--------------------------------)[![Angelica Lo Duca](../Images/45aa2e2e504bb3af6d3b8009dc6f030e.png)](https://alod83.medium.com/?source=post_page---byline--eb08472ee9be--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--eb08472ee9be--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--eb08472ee9be--------------------------------) [Angelica Lo Duca](https://alod83.medium.com/?source=post_page---byline--eb08472ee9be--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--eb08472ee9be--------------------------------) ·阅读时间：7分钟·2024年2月15日

--

![](../Images/cd7bf122ca7a1d589ef6cfde8246bd6c.png)

图片由[Farzad](https://unsplash.com/@euwars?utm_source=medium&utm_medium=referral)拍摄，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

最近，我一直在做一个姓氏匹配的项目。我有一个包含姓氏的数据集，其中一些可能包含拼写错误。我的目标是识别哪些姓氏可能是错误的，然后进行修正。为此，我考虑了计算所有姓氏之间的相似性，然后将那些相似性超过某一阈值的姓氏组合起来。但是，我可以使用哪些标准来计算相似性呢？

在本文中，我介绍了三种计算两个字符串（因此也可以计算两个姓氏）之间相似性的方法：

+   莱文斯坦距离

+   贾罗相似度

+   美国 Soundex 编码

作为案例研究，我们将使用一个包含 30 个虚构姓氏的数据集，该数据集由 ChatGPT 使用以下提示生成：*生成一个包含 30 个姓氏的列表，其中 10 个姓氏包含拼写错误*。在[这个链接](https://chat.openai.com/share/a43c5dad-fe0e-4fe2-9fa2-d98217b26a50)中，你可以找到用于生成该数据集的完整聊天记录。

我们将使用 jellyfish Python 库实现一个姓氏匹配示例，你…
