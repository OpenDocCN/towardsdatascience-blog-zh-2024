# 你的选票会决定下一任总统吗？

> 原文：[`towardsdatascience.com/will-your-vote-decide-the-next-president-0875a4927c75?source=collection_archive---------3-----------------------#2024-10-15`](https://towardsdatascience.com/will-your-vote-decide-the-next-president-0875a4927c75?source=collection_archive---------3-----------------------#2024-10-15)

## 模拟你的单一选票在 11 月的选举中扭转结果的概率

[](https://medium.com/@jarom.hulet?source=post_page---byline--0875a4927c75--------------------------------)![Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page---byline--0875a4927c75--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0875a4927c75--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0875a4927c75--------------------------------) [Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page---byline--0875a4927c75--------------------------------)

·发布于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--0875a4927c75--------------------------------) ·阅读时间 18 分钟·2024 年 10 月 15 日

--

![](img/a2a0ea20e016146a5be9f5a1fde13055.png)

图片来源：Element5 Digital, Pexels.com

好吧，选举季节已经全面展开——我告诉你，假如你还没注意到的话，电视广告和院子里的标语足够让人不耐烦。拥有投票权是一项美妙的机会，而许多世界公民却没有这种权利。然而，我常常想，我的单一选票到底能有多大的影响。在这篇文章中，我将尝试使用我最喜欢的工具之一——**模拟**，来回答这个问题！

我喜欢使用模拟来解决问题——今年早些时候我写了一系列关于模拟的文章——如果你感兴趣，可以看看。

![Jarom Hulet](img/44595b6052adc15ed442ed9da5c7bb33.png)

[Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page-----0875a4927c75--------------------------------)

## 数据模拟

[查看列表](https://medium.com/@jarom.hulet/list/data-simulation-911e0d7f9188?source=post_page-----0875a4927c75--------------------------------)6 个故事![](img/41832782100de81162ff634193c55f3b.png)![](img/17ec782aaf4e9c3b7ae0cd6fd575b558.png)![](img/b2cd2b7f83183f81d970c333e76f4a41.png)

在这篇文章中，我们将涵盖以下话题：

1.  设置模拟

1.  模拟你的选票改变你所在州的选举结果

1.  模拟你所在州改变整个选举的结果

1.  估算你的选票决定总统的总体概率
