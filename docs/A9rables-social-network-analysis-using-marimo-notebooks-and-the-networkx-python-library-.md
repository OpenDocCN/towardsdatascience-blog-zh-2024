# 《悲惨世界》社交网络分析：使用Marimo笔记本和NetworkX Python库

> 原文：[https://towardsdatascience.com/les-mis%C3%A9rables-social-network-analysis-using-marimo-notebooks-and-the-networkx-python-library-%EF%B8%8F-%EF%B8%8F-3f433216412f?source=collection_archive---------6-----------------------#2024-10-31](https://towardsdatascience.com/les-mis%C3%A9rables-social-network-analysis-using-marimo-notebooks-and-the-networkx-python-library-%EF%B8%8F-%EF%B8%8F-3f433216412f?source=collection_archive---------6-----------------------#2024-10-31)

## 数据科学

## 使用NetworkX Python库构建Marimo笔记本，揭示维克多·雨果杰作中的隐藏结构

[](https://medium.com/@m.mouschoutzi?source=post_page---byline--3f433216412f--------------------------------)[![玛丽亚·穆斯丘齐博士](../Images/66f8cf036b6e4db2e24eae3e9f492db8.png)](https://medium.com/@m.mouschoutzi?source=post_page---byline--3f433216412f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3f433216412f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3f433216412f--------------------------------) [玛丽亚·穆斯丘齐博士](https://medium.com/@m.mouschoutzi?source=post_page---byline--3f433216412f--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3f433216412f--------------------------------) ·阅读时间：14分钟·2024年10月31日

--

![](../Images/f9526363e24f76805a724fa8abc8c7a0.png)

该图像由作者使用GPT-4o创建

在这篇文章中，我将带你通过使用NetworkX Python库和*悲惨世界*社交网络数据集，构建一个互动的Marimo笔记本进行社交网络分析。通过实施社交网络分析技术，我们可以深入了解小说中不同角色之间的联系如何塑造故事，并揭示正义、爱情和牺牲等主题，这些主题定义了小说的叙事。

# 那《悲惨世界》怎么样？💔

当然，[*悲惨世界*](https://en.wikipedia.org/wiki/Les_Mis%C3%A9rables)是有史以来最伟大的故事之一。我几乎热爱它的每一个版本和变化——无论是书籍、电影、电视剧，还是音乐剧——全部都喜欢。

《悲惨世界》写于1862年，探讨了19世纪法国社会和文化框架下的正义、救赎、爱与牺牲等概念。故事的叙述围绕几位不同角色展开，其中最为突出的是让·瓦尔让，一位寻求救赎的前罪犯，以及警察局长贾维尔，他是……
