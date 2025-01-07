# 绘制宝可梦世界：基于栖息地的遭遇网络分析

> 原文：[https://towardsdatascience.com/mapping-the-pok%C3%A9mon-world-a-network-analysis-of-habitat-based-encounters-9b8e5fe4db0a?source=collection_archive---------4-----------------------#2024-06-14](https://towardsdatascience.com/mapping-the-pok%C3%A9mon-world-a-network-analysis-of-habitat-based-encounters-9b8e5fe4db0a?source=collection_archive---------4-----------------------#2024-06-14)

## 本文将介绍在 Python 中进行网络分析，并通过使用来自 PokeApi 的宝可梦数据进行实际示例

[](https://medium.com/@jaingle77?source=post_page---byline--9b8e5fe4db0a--------------------------------)[![Jacob Ingle](../Images/f2543a1c4b12ef28a83c30c1667ee032.png)](https://medium.com/@jaingle77?source=post_page---byline--9b8e5fe4db0a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9b8e5fe4db0a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9b8e5fe4db0a--------------------------------) [Jacob Ingle](https://medium.com/@jaingle77?source=post_page---byline--9b8e5fe4db0a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9b8e5fe4db0a--------------------------------) ·15分钟阅读·2024年6月14日

--

![](../Images/480e0de76a48647d9c891d17f1789913.png)

图片由[Michael Rivera](https://unsplash.com/@gojomike?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍

网络分析是一种强大而简便的方法，用于有意义地理解各种实体之间的关系、联系和分组。从社交媒体的角度来看，人们可以通过网络分析了解哪些个人资料有最有意义的联系；电子商务公司可以利用其网站分析来理解产品之间的浏览关系；公司可以分析电子邮件通信，以了解哪些团队经常一起工作，等等！只要你的数据具有某种网络特征，网络分析无疑可以应用。

本文将介绍通过 Python 进行网络分析，您可以从网络分析中获取的实际见解，以及一个与热门 TV 和游戏系列《宝可梦》相关的有趣项目！

# 什么是网络分析

![](../Images/ab11887e7247d15ab79d1bb86eed86fe.png)

图片由DALL-E生成
