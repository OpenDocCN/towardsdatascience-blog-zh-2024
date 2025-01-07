# 6分钟掌握分布图

> 原文：[https://towardsdatascience.com/master-dispersion-plots-in-6-minutes-241d15f22702?source=collection_archive---------5-----------------------#2024-03-03](https://towardsdatascience.com/master-dispersion-plots-in-6-minutes-241d15f22702?source=collection_archive---------5-----------------------#2024-03-03)

## 快速成功数据科学

## 使用NLTK学习图形化文本分析

[](https://medium.com/@lee_vaughan?source=post_page---byline--241d15f22702--------------------------------)[![Lee Vaughan](../Images/9f6b90bb76102f438ab0b9a4a62ffa3f.png)](https://medium.com/@lee_vaughan?source=post_page---byline--241d15f22702--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--241d15f22702--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--241d15f22702--------------------------------) [Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--241d15f22702--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--241d15f22702--------------------------------) ·6分钟阅读·2024年3月3日

--

![](../Images/9f53c03c37b4376180f658330bf0db4a.png)

福尔摩斯（由DALL-E3创作）

*自然语言工具包（NLTK）*附带一个有趣的功能，叫做*分布图*，它可以让你展示一个单词在文本中的位置。更具体来说，它绘制了一个单词的出现次数与从语料库开始计算的词数之间的关系。

这是《福尔摩斯探案集》中的主要角色的分布图示例，来自小说《巴斯克维尔的猎犬》：

![](../Images/2ca7c98be21aca069ef232d8f4494e8e.png)

《巴斯克维尔的猎犬》中的主要角色的分布图（按作者）

垂直的蓝色勾号表示文本中目标单词的位置。每一行覆盖整个语料库，从头到尾。

如果你熟悉《巴斯克维尔的猎犬》——如果你还没看过，我不会剧透——你就会欣赏到霍尔姆斯在中间部分的稀疏出现、莫提默的迟到登场，以及巴里莫尔、塞尔登和猎犬的重叠。

分布图还有更实际的应用。例如，假设你是一个数据科学家，正在与律师助理一起处理一起涉及内幕交易的刑事案件。你需要找出被告是否在案发前与董事会成员联系过……
