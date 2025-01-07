# 模型漂移介绍与概念

> 原文：[`towardsdatascience.com/model-drift-introduction-and-concepts-e32c5305da2a?source=collection_archive---------3-----------------------#2024-06-22`](https://towardsdatascience.com/model-drift-introduction-and-concepts-e32c5305da2a?source=collection_archive---------3-----------------------#2024-06-22)

## 了解机器学习模型漂移背后的一些概念，并理解为什么 MLOps 在今天的世界中如此重要。

[](https://ivopbernardo.medium.com/?source=post_page---byline--e32c5305da2a--------------------------------)![Ivo Bernardo](https://ivopbernardo.medium.com/?source=post_page---byline--e32c5305da2a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e32c5305da2a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e32c5305da2a--------------------------------) [Ivo Bernardo](https://ivopbernardo.medium.com/?source=post_page---byline--e32c5305da2a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e32c5305da2a--------------------------------) ·6 分钟阅读·2024 年 6 月 22 日

--

![](img/0b9938407dfc9c98df78699e1a26ccce.png)

模型会发生变化，因为世界在变化——图片来源：[arptrastogi](https://unsplash.com/pt-br/@arptrastogi) 通过 Unsplash.com

税收、死亡和模型漂移是生活中唯一的三大确定性。好吧，我可能在这句格言中加入了最后一个，但事实是所有模型都会遭遇衰退。

开发机器学习模型后，你总会看到相同的模式发展：

+   在开发过程中，模型在测试集上的表现是预期的。

+   模型在投入生产后表现不同（通常，表现稍差）。

+   模型的性能随着时间的推移而下降。

几年后，你的模型表现很可能比最初开发时差得多。**这可能由多种原因引起，但根本原因是世界在变化。**

**当世界变化时，我们用来表示现实信息的数据也会发生变化。** 潜在的数据分布发生偏移，这必然会影响我们的机器学习模型如何学习和表现。

在这篇博客文章中，我们将探讨一些情况示例，其中世界的潜在变化如何影响你的模型……
