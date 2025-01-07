# 在机器学习项目中利用 Python 继承

> 原文：[`towardsdatascience.com/leverage-python-inheritance-in-ml-projects-52e7e16401ab?source=collection_archive---------7-----------------------#2024-11-21`](https://towardsdatascience.com/leverage-python-inheritance-in-ml-projects-52e7e16401ab?source=collection_archive---------7-----------------------#2024-11-21)

![](img/7171f5b4e0b03721a27f56b25ad645e4.png)

图片由[Milad Fakurian](https://unsplash.com/@fakurian?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 学习如何实现编码最佳实践，以避免技术债务

[](https://medium.com/@marcellopoliti?source=post_page---byline--52e7e16401ab--------------------------------)![Marcello Politi](https://medium.com/@marcellopoliti?source=post_page---byline--52e7e16401ab--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--52e7e16401ab--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--52e7e16401ab--------------------------------) [Marcello Politi](https://medium.com/@marcellopoliti?source=post_page---byline--52e7e16401ab--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--52e7e16401ab--------------------------------) ·阅读时间 5 分钟·2024 年 11 月 21 日

--

# 介绍

许多接触机器学习的人没有计算机工程的扎实背景，当他们需要处理一个实际产品时，他们的代码可能会很凌乱，且难以管理。这就是为什么我总是强烈建议学习使用编码最佳实践，这样你就能在团队中顺利工作，并提升你所从事的项目。今天，我想谈谈 Python 的继承，并展示一些简单的示例，说明如何在机器学习领域中使用它。

> 在[软件开发](https://en.wikipedia.org/wiki/Software_development)和其他[信息技术](https://en.wikipedia.org/wiki/Information_technology)领域，**技术债务**（也称为**设计债务**或**代码债务**）是未来重新工作所隐含的成本，因为某个解决方案优先考虑快速实现而非长期设计。

如果你有兴趣了解更多关于设计模式的内容，你可能会对[我之前的文章](https://medium.com/towards-data-science/design-patterns-with-python-for-machine-learning-engineers-prototype-fcc1358a862d)感兴趣。

# Python 继承

继承不仅是 Python 的概念，还是面向对象编程中的一个通用概念。因此，在本教程中，我们需要处理类和对象，这是在 Python 中相对于其他语言（如 Java）不常用的编程范式。
