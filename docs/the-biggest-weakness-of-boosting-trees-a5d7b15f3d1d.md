# 增强树的最大弱点

> 原文：[https://towardsdatascience.com/the-biggest-weakness-of-boosting-trees-a5d7b15f3d1d?source=collection_archive---------2-----------------------#2024-02-12](https://towardsdatascience.com/the-biggest-weakness-of-boosting-trees-a5d7b15f3d1d?source=collection_archive---------2-----------------------#2024-02-12)

## 为什么分布漂移真的会伤害你的模型

[](https://medium.com/@jacky.kaub?source=post_page---byline--a5d7b15f3d1d--------------------------------)[![Jacky Kaub](../Images/e66c699ee5a9d5bbd58a1a72d688234a.png)](https://medium.com/@jacky.kaub?source=post_page---byline--a5d7b15f3d1d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a5d7b15f3d1d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a5d7b15f3d1d--------------------------------) [Jacky Kaub](https://medium.com/@jacky.kaub?source=post_page---byline--a5d7b15f3d1d--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a5d7b15f3d1d--------------------------------) ·10分钟阅读·2024年2月12日

--

![](../Images/586edd3d27e719508579b7f9a3040cd3.png)

图片由[Sebastian Unrau](https://unsplash.com/@sebastian_unrau?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍

我已经做了五年数据科学家，在这五年中，我有机会参与了各种类型的无数项目。像许多数据科学家一样，在处理表格数据集时，我开始养成了一种反应模式：“如果是表格数据，特征工程 + 增强算法就能解决问题！”然后我就不再追问更多问题。

的确，增强算法在表格数据领域已经长时间处于技术前沿，以至于它们的优势变得难以质疑。

在本文中，我们将深入探讨增强算法背后的某些有趣的理论部分，特别是它们的核心组成部分——决策树，并理解在面对数据漂移时，使用增强算法时应该特别小心的场景。

# 通过线性回归的视角简要讨论数据漂移

数据漂移，简单来说，就是你的数据分布随时间变化，这可能会影响你的机器学习模型。
