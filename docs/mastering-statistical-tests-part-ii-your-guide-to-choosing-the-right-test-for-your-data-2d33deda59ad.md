# 精通统计检验（第二部分）

> 原文：[https://towardsdatascience.com/mastering-statistical-tests-part-ii-your-guide-to-choosing-the-right-test-for-your-data-2d33deda59ad?source=collection_archive---------7-----------------------#2024-05-27](https://towardsdatascience.com/mastering-statistical-tests-part-ii-your-guide-to-choosing-the-right-test-for-your-data-2d33deda59ad?source=collection_archive---------7-----------------------#2024-05-27)

## 您选择适合数据的检验方法的指南

[](https://medium.com/@shreef.nasser?source=post_page---byline--2d33deda59ad--------------------------------)[![Sheref Nasereldin, Ph.D.](../Images/91c0848fe85633bec0c0004004a64a62.png)](https://medium.com/@shreef.nasser?source=post_page---byline--2d33deda59ad--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2d33deda59ad--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2d33deda59ad--------------------------------) [Sheref Nasereldin, Ph.D.](https://medium.com/@shreef.nasser?source=post_page---byline--2d33deda59ad--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2d33deda59ad--------------------------------) ·阅读时长：12分钟·2024年5月27日

--

![](../Images/8b736364dd2aa26daefbb7f278888d99.png)

图片来源：[Annie Spratt](https://unsplash.com/@anniespratt?utm_source=medium&utm_medium=referral) 来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在[第一部分](https://medium.com/towards-data-science/statistical-tests-demystified-how-to-choose-the-best-test-for-your-data-part-i-688a4b2a23b7)中，我探讨了根据一些标准对统计检验进行分类的方法，例如独立变量的数量（我只关注了只有一个独立变量的情境）和因变量（同样，我只考虑了一个因变量），以及独立变量中的各个水平和这些水平的独立性。在此基础上，第二部分将分析扩展到涉及独立变量中两个以上组别（水平）的情境，甚至增加独立变量的数量。在这里，我将深入探讨专门为这些复杂配置量身定制的检验方法，并提供其有效应用的见解。

[](/statistical-tests-demystified-how-to-choose-the-best-test-for-your-data-part-i-688a4b2a23b7?source=post_page-----2d33deda59ad--------------------------------) [## 精通统计检验

### 选择适合您数据的检验方法指南（第一部分）

towardsdatascience.com](/statistical-tests-demystified-how-to-choose-the-best-test-for-your-data-part-i-688a4b2a23b7?source=post_page-----2d33deda59ad--------------------------------)

## 11- 单因素方差分析（ANOVA）

该测试用于确定三个或更多独立组之间的均值是否存在显著差异。它有助于确认是否至少有一个组的均值与…不同。
