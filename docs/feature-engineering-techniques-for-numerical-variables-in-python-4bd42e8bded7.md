# 适用于 Python 中数值变量的特征工程技术

> 原文：[`towardsdatascience.com/feature-engineering-techniques-for-numerical-variables-in-python-4bd42e8bded7?source=collection_archive---------2-----------------------#2024-09-24`](https://towardsdatascience.com/feature-engineering-techniques-for-numerical-variables-in-python-4bd42e8bded7?source=collection_archive---------2-----------------------#2024-09-24)

## *学习如何使用 Sklearn、Numpy 和 Python，将数值转化为对预测模型有用的信息，掌握最有用的特征工程技术*

[](https://medium.com/@theDrewDag?source=post_page---byline--4bd42e8bded7--------------------------------)![Andrea D'Agostino](https://medium.com/@theDrewDag?source=post_page---byline--4bd42e8bded7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4bd42e8bded7--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4bd42e8bded7--------------------------------) [Andrea D'Agostino](https://medium.com/@theDrewDag?source=post_page---byline--4bd42e8bded7--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4bd42e8bded7--------------------------------) ·阅读时间：18 分钟·2024 年 9 月 24 日

--

![](img/87c17e4e9a7ed1dc6f5c12abd384f7cd.png)

图片来源：[ThisisEngineering](https://unsplash.com/@thisisengineering?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

特征工程是机器学习流程中的一个关键步骤，**在这个过程中，原始数据被转化为更有意义的特征，从而帮助模型更好地理解数据中的关系。**

特征工程通常意味着对现有数据进行变换，替换或创建新的数据，这些数据在机器学习和数据科学的背景下用于训练模型，借助这些变换，模型能够更好地执行任务。

在本文中，我们将探讨使用 Python 的 Scikit-Learn 库（可以通过[BSD 3-Clause License](https://github.com/scikit-learn/scikit-learn?tab=BSD-3-Clause-1-ov-file)进行使用）、Numpy 等库来处理数值数据的高级**特征工程技术**，以提高机器学习模型的效果。

总结来说，通过阅读本文，你将学习到：

+   **一套强大的特征工程技术，适用于数值数据**，来自 Scikit-Learn、Numpy 和 Scipy 工具包，用于提升机器学习模型的性能
