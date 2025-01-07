# 赚取与学习：解决一个钓鱼启发的多臂赌博机问题

> 原文：[`towardsdatascience.com/earn-vs-learn-solving-a-fishing-inspired-multi-armed-bandit-problem-924ae0d7947e?source=collection_archive---------7-----------------------#2024-05-15`](https://towardsdatascience.com/earn-vs-learn-solving-a-fishing-inspired-multi-armed-bandit-problem-924ae0d7947e?source=collection_archive---------7-----------------------#2024-05-15)

## 探索五种算法以平衡赚取/学习的权衡

[](https://medium.com/@jarom.hulet?source=post_page---byline--924ae0d7947e--------------------------------)![Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page---byline--924ae0d7947e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--924ae0d7947e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--924ae0d7947e--------------------------------) [Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page---byline--924ae0d7947e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--924ae0d7947e--------------------------------) ·阅读时间 17 分钟·2024 年 5 月 15 日

--

![](img/b13d15fd8689c395275ad2860584d961.png)

图片来自作者

我的目标是让你从这篇文章中获得以下内容：

1.  对于什么构成了多臂赌博机问题有一个良好的理解

1.  了解如何解决多臂赌博机问题（需要考虑的因素、常见算法的示例，以及使用 Python 代码的模拟数据）

几年前，我读了一本很棒的书，叫做《Algorithms to Live By》，作者是 Brian Christian 和 Tom Griffiths。这本书的主要概念是，我们在日常生活中面临的许多决策问题，与数据专业人员在工作中面临的问题类型相同。我在生活中看到的多臂赌博机问题的实例比其他任何问题都多。它到处都能看到！

我在我的休闲爱好——钓鱼中看到了这个问题。快速声明——我钓鱼技术很差——但我仍然很享受它。我常常会想，我应该使用哪种诱饵才能钓到尽可能多的鱼。当我使用一种诱饵时，我总是试图决定是否有另一种诱饵可能会钓到更多的鱼。这就是一个多臂赌博机问题！

我觉得展示各种解决钓鱼启发的多臂赌博机问题的方法既有趣又富有启发性…
