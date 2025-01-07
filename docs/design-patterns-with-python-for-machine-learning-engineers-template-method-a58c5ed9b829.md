# 《Python设计模式与机器学习工程师：模板方法》

> 原文：[https://towardsdatascience.com/design-patterns-with-python-for-machine-learning-engineers-template-method-a58c5ed9b829?source=collection_archive---------3-----------------------#2024-12-24](https://towardsdatascience.com/design-patterns-with-python-for-machine-learning-engineers-template-method-a58c5ed9b829?source=collection_archive---------3-----------------------#2024-12-24)

![](../Images/b025825fc9cf00dbcc9c83c44d4f1700.png)

图片由[Pawel Czerwinski](https://unsplash.com/@pawel_czerwinski?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 学习如何使用模板设计模式来增强你的代码

[](https://medium.com/@marcellopoliti?source=post_page---byline--a58c5ed9b829--------------------------------)[![Marcello Politi](../Images/484e44571bd2e75acfe5fef3146ab3c2.png)](https://medium.com/@marcellopoliti?source=post_page---byline--a58c5ed9b829--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a58c5ed9b829--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a58c5ed9b829--------------------------------) [Marcello Politi](https://medium.com/@marcellopoliti?source=post_page---byline--a58c5ed9b829--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a58c5ed9b829--------------------------------) ·4分钟阅读·2024年12月24日

--

## 介绍

最近我一直在进行几个大语言模型（LLM）的领域特定微调工作。这项任务的第一部分，也许是最重要的一部分，就是收集、抓取和清理文本数据，以供LLM使用。我注意到我的代码变得很乱，重复了很多，因为每当识别到一个数据源时，我都在从头编写一个脚本，而这个脚本和代码库中的其他脚本有很多相似之处。我完全没有遵循“***不要重复自己***”（DRY）原则。这就是为什么我决定实现模板设计模式，使我的代码库更加优雅和高效。

## 模板设计模式

我在这里不再重复设计模式是什么，以及我们如何根据功能对设计模式进行分类，因为我已经写过很多相关文章。如果你有兴趣阅读我以前关于这个主题的文章，我会在最后提供一些参考文献。

在这篇文章中，我将展示一个**与数据处理相关的例子**。假设在我们的项目中，我们需要处理不同类型的数据，这些数据我们希望进行分析。其中一些数据是…
