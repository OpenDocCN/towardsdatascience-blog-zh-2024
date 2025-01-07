# 随机漫步是奇异且美丽的

> 原文：[https://towardsdatascience.com/random-walks-are-strange-and-beautiful-0c4813aca6bc?source=collection_archive---------7-----------------------#2024-03-27](https://towardsdatascience.com/random-walks-are-strange-and-beautiful-0c4813aca6bc?source=collection_archive---------7-----------------------#2024-03-27)

## 一场穿越维度和生活的旅程

[](https://medium.com/@mmsbrggr?source=post_page---byline--0c4813aca6bc--------------------------------)[![Marcel Moosbrugger](../Images/c814f809a274dfc8191264d33c73817e.png)](https://medium.com/@mmsbrggr?source=post_page---byline--0c4813aca6bc--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0c4813aca6bc--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--0c4813aca6bc--------------------------------) [Marcel Moosbrugger](https://medium.com/@mmsbrggr?source=post_page---byline--0c4813aca6bc--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0c4813aca6bc--------------------------------) ·阅读时长9分钟·2024年3月27日

--

![](../Images/ac5463ceec6fb9d923da537361c10016.png)

图片由[Jezael Melgoza](https://unsplash.com/de/@jezar)提供，来源于[Unsplash](https://unsplash.com/de/fotos/menschen-die-auf-der-strasse-in-der-nahe-von-gut-beleuchteten-gebauden-gehen-layMbSJ3YOE)

想象一下，你发现自己被蒙上眼睛，站在一个陌生且密集的城市中心。每当你走到一个十字路口时，硬币的掷落决定你接下来的步骤：向左、向右、向前或向后。没有视觉指引，只有随机性作为你的唯一伴侣，你开始了一个**不可预测的旅程**。

这本质上捕捉了*随机漫步*的精神，这是概率论中的一个强大概念，比起在城市中蒙着眼睛拿着硬币走路，它要有用得多。物理学家使用随机漫步来描述**粒子的运动**，并在从**生物学到社会科学**的各个领域中都有应用。理解随机漫步使数据科学家能够建模、模拟并预测来自多个不同领域的随机过程。

此外，在**强化学习**中，智能体可以进行随机漫步，以**探索其环境**并获取有关其行动影响的信息。

简而言之，随机漫步极其多用途。但那是另一个故事。

除了应用之外，随机漫步本身非常迷人。即使没有背后的数学，我们也可以欣赏它们为我们打开的美丽、复杂且充满谜团的世界。如果你在城市中随机漫步足够长时间，并回溯你的足迹，**你的路径揭示了一个**…
