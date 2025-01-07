# 机器学习中的线性与非线性指南

> 原文：[`towardsdatascience.com/a-guide-to-linearity-and-nonlinearity-in-machine-learning-0c22845fdff3?source=collection_archive---------0-----------------------#2024-10-28`](https://towardsdatascience.com/a-guide-to-linearity-and-nonlinearity-in-machine-learning-0c22845fdff3?source=collection_archive---------0-----------------------#2024-10-28)

## …以及它们在决策边界、嵌入、动力学系统和下一代大语言模型中的作用

[](https://manuel-brenner.medium.com/?source=post_page---byline--0c22845fdff3--------------------------------)![Manuel Brenner](https://manuel-brenner.medium.com/?source=post_page---byline--0c22845fdff3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--0c22845fdff3--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0c22845fdff3--------------------------------) [Manuel Brenner](https://manuel-brenner.medium.com/?source=post_page---byline--0c22845fdff3--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--0c22845fdff3--------------------------------) ·阅读时间：21 分钟·2024 年 10 月 28 日

--

> “以牙还牙，以眼还眼。”
> 
> — *报应法则，《汉谟拉比法典》*

著名的*报应法则*是一种比例法则。你夺走我的眼睛，我夺走你的。你夺走我的牙齿，我夺走你的（当时做巴比伦的牙医一定很辛苦）。

该法律并非为了促进暴力，而是旨在限制暴力。*报应法则*设想了一个可以用线性方程来描述的一切的法律世界：每个罪行都会产生与其输入成比例的输出。由于对犯罪的惩罚与罪行成比例，它避免了过度的报复和暴力爆发，这些暴力将一切摧毁殆尽。

在报应的世界之外，线性在我们理解世界的过程中扮演着重要角色：在线性系统中，一切都是可以理解的。没有混乱，没有复杂的数学。所有科学家整天要做的就是解决这些类型的方程：

**每一个动作都有一个相等且相反的反应。**

— *牛顿第三定律*

不幸的是，我们所处的现实远非这种线性理想世界。历史上充斥着世界以高度不成比例的方式回应小事的例子：布拉格窗户事件引发了三十年战争……
