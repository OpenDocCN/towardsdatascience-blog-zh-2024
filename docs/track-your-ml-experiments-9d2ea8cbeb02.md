# 跟踪你的机器学习实验

> 原文：[https://towardsdatascience.com/track-your-ml-experiments-9d2ea8cbeb02?source=collection_archive---------8-----------------------#2024-03-29](https://towardsdatascience.com/track-your-ml-experiments-9d2ea8cbeb02?source=collection_archive---------8-----------------------#2024-03-29)

## 使用Neptune.ai跟踪Python中的实验指南

[](https://medium.com/@pelletierhaden?source=post_page---byline--9d2ea8cbeb02--------------------------------)[![Haden Pelletier](../Images/8f73fc8222e783883c4ebcaee14513e0.png)](https://medium.com/@pelletierhaden?source=post_page---byline--9d2ea8cbeb02--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9d2ea8cbeb02--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9d2ea8cbeb02--------------------------------) [Haden Pelletier](https://medium.com/@pelletierhaden?source=post_page---byline--9d2ea8cbeb02--------------------------------)

·发布在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9d2ea8cbeb02--------------------------------) ·阅读时间：7分钟·2024年3月29日

--

![](../Images/1c33444e15f12e4cf5a5edd7d2b09982.png)

图片来自[Alex Kondratiev](https://unsplash.com/@alexkondratiev?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

每位数据科学家都对实验有所了解。

你知道流程的。你获取一个数据集，将其加载到Jupyter笔记本中，进行探索，预处理数据，拟合一个或两个基准模型，然后训练一个初步的最终模型，例如XGBoost。第一次可能没有调整超参数并且包含了20个特征。然后，你检查你的误差指标。

它们看起来还不错，但也许你的模型有些过拟合。所以你决定调整一些正则化参数（例如最大深度），以降低模型的复杂性，并重新运行它。

你从上次运行中看到了一些改进，但也许你还想：

+   添加更多特征

+   执行特征选择并去除一些特征

+   尝试为你的特征使用不同的标准化方法

+   调整不同/更多的超参数

随着你想要运行的不同种类的测试增加，记住哪些“实验”组合实际上产生了最佳结果变得越来越困难。你只能运行一个笔记本那么多次，打印出结果，然后复制/粘贴到Google文档中，直到你感到沮丧。
