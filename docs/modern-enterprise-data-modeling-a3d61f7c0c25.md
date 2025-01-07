# 现代企业数据建模

> 原文：[`towardsdatascience.com/modern-enterprise-data-modeling-a3d61f7c0c25?source=collection_archive---------3-----------------------#2024-07-20`](https://towardsdatascience.com/modern-enterprise-data-modeling-a3d61f7c0c25?source=collection_archive---------3-----------------------#2024-07-20)

## 如何解决浅薄、过时的模型的不足，并使您的建模策略具有未来适应性

[](https://medium.com/@bernd.wessely?source=post_page---byline--a3d61f7c0c25--------------------------------)![Bernd Wessely](https://medium.com/@bernd.wessely?source=post_page---byline--a3d61f7c0c25--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a3d61f7c0c25--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a3d61f7c0c25--------------------------------) [Bernd Wessely](https://medium.com/@bernd.wessely?source=post_page---byline--a3d61f7c0c25--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a3d61f7c0c25--------------------------------) ·阅读时长 11 分钟·2024 年 7 月 20 日

--

![](img/0b7c6f6e4b2e99b0b4849cf09d6f3333.png)

图片来自 [Justin Chrn](https://unsplash.com/@justinchrn?utm_source=medium&utm_medium=referral) ，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我从事数据建模已经超过 30 年，创建了多种数据模型（3NF、维度模型、集成模型（锚模型、数据金库）、图形模型等），主要用于分析系统。然而，这些模型中的许多也逐渐变得过时或过时了。有时候感觉就像是那个不幸的西西弗斯，他不断地把大石头推上山顶，却在某个时刻意识到这又是徒劳的。

很长一段时间，我一直相信，必须有可能为一家公司集中建模一个共同而完整的业务视图。毕竟，长期从事建模过程的业务人员，应该知道公司内部发生了什么吧？好吧，公司越小，我离目标就越近。但老实说，最终每个模型都只是一个近似值——一个试图反映不断变化现实的静态视图。

但是，即使创建这样的模型非常费力，我们绝对无法在没有它的情况下取得成功。现代数据驱动的企业基于从数据中提取价值的核心理念。然而，事实是数据本身没有价值。我们需要使用…
