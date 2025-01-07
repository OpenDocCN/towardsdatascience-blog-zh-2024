# 做交叉验证时需要避免的两个常见陷阱

> 原文：[https://towardsdatascience.com/two-common-pitfalls-to-avoid-when-doing-cross-validation-c68ed79c0e4e?source=collection_archive---------1-----------------------#2024-05-24](https://towardsdatascience.com/two-common-pitfalls-to-avoid-when-doing-cross-validation-c68ed79c0e4e?source=collection_archive---------1-----------------------#2024-05-24)

## 以及你需要用来应对这些问题的技巧

[](https://medium.com/@mattchapmanmsc?source=post_page---byline--c68ed79c0e4e--------------------------------)[![Matt Chapman](../Images/7511deb8d9ed408ece21031f6614c532.png)](https://medium.com/@mattchapmanmsc?source=post_page---byline--c68ed79c0e4e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c68ed79c0e4e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c68ed79c0e4e--------------------------------) [Matt Chapman](https://medium.com/@mattchapmanmsc?source=post_page---byline--c68ed79c0e4e--------------------------------)

·发布于[数据科学进阶](https://towardsdatascience.com/?source=post_page---byline--c68ed79c0e4e--------------------------------) ·阅读时长8分钟·2024年5月24日

--

交叉验证是数据科学家必须掌握的一项重要技术，但它也容易被误用。

在本文中，我将重点介绍我经常看到的两种错误，以及你需要掌握的概念来应对这些错误：

1.  嵌套交叉验证

1.  时间序列交叉验证

学习这些技巧帮助我获得了我的第一份数据科学工作，如果你能掌握它们，你将避免在构建机器学习模型时犯傻错误。

# **但是首先，回顾一下：交叉验证的意义是什么？**

机器学习的基本理念是：在一个“训练”数据集上拟合模型，并在一个单独的保留“测试”数据集上评估模型的表现（这应该模拟你模型在现实世界中的表现）：

```py
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import roc_auc_score
from sklearn.datasets import make_classification

# Example dataset
X, y = make_classification(n_samples=1000, n_features=20, random_state=42)

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

clf = LogisticRegression()
clf.fit(X_train, y_train)
y_pred = clf.predict(X_test)

rocauc = roc_auc_score(y_test, y_pred)
```
