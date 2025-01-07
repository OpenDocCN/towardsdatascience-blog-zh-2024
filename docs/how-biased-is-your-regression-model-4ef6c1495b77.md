# 你的回归模型有多偏差？

> 原文：[https://towardsdatascience.com/how-biased-is-your-regression-model-4ef6c1495b77?source=collection_archive---------0-----------------------#2024-09-13](https://towardsdatascience.com/how-biased-is-your-regression-model-4ef6c1495b77?source=collection_archive---------0-----------------------#2024-09-13)

![](../Images/f1347896058cc61e348cccbe94cf59b8.png)

图片来源：作者

## 深入探讨回归模型中的偏差原因、影响及补救措施

[](https://timeseriesreasoning.medium.com/?source=post_page---byline--4ef6c1495b77--------------------------------)[![Sachin Date](../Images/bd023298b414caf88f79b00ef032d065.png)](https://timeseriesreasoning.medium.com/?source=post_page---byline--4ef6c1495b77--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4ef6c1495b77--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4ef6c1495b77--------------------------------) [Sachin Date](https://timeseriesreasoning.medium.com/?source=post_page---byline--4ef6c1495b77--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4ef6c1495b77--------------------------------) ·阅读时长27分钟·2024年9月13日

--

这一包花生M&M糖的重量是45克。

![](../Images/a5d99198a0234cd2697ac89d17ca0686.png)

(图片来源：作者)

Mars公司可能已经精确地定价了这一包。但Mars是如何知道要将多少颗花生M&M糖放入一个45克重的包裹中的呢？

我们可以简单地将45克除以一颗花生M&M糖的平均重量，然后四舍五入到最接近的整数（呃！）。

但这又引出了另一个问题：

一颗花生M&M糖的平均重量是多少？

到今年年底，Mars公司工厂将生产出约1500亿颗M&M糖。明年生产的数量会更多，未来几年也会继续增加。那么，你如何知道一颗M&M糖的平均重量呢？

*顺便说一下，究竟是谁在吃掉这些M&M糖？*

无论如何，计算一颗M&M糖的平均重量——所谓的**总体均值**——几乎是不可能的。

如果我们无法得知它的值，那么我们必须估计它，而估计总是伴随着误差。
