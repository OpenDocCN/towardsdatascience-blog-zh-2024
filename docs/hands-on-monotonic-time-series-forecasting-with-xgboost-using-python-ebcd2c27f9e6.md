# 使用 XGBoost 进行单调时间序列预测，使用 Python

> 原文：[`towardsdatascience.com/hands-on-monotonic-time-series-forecasting-with-xgboost-using-python-ebcd2c27f9e6?source=collection_archive---------1-----------------------#2024-03-29`](https://towardsdatascience.com/hands-on-monotonic-time-series-forecasting-with-xgboost-using-python-ebcd2c27f9e6?source=collection_archive---------1-----------------------#2024-03-29)

## 这是如何在预测场景中使用 XGBoost，从理论到实践

[](https://piero-paialunga.medium.com/?source=post_page---byline--ebcd2c27f9e6--------------------------------)![Piero Paialunga](https://piero-paialunga.medium.com/?source=post_page---byline--ebcd2c27f9e6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ebcd2c27f9e6--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ebcd2c27f9e6--------------------------------) [Piero Paialunga](https://piero-paialunga.medium.com/?source=post_page---byline--ebcd2c27f9e6--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ebcd2c27f9e6--------------------------------) ·9 分钟阅读·2024 年 3 月 29 日

--

![](img/f5308a685cb6d789298dc2213c19b4cc.png)

该图由作者使用 DALL·E-3 制作

几个月前，我在一个研究项目中遇到了一个涉及时间序列的问题。

这个问题相当简单：

> “从这个时间序列开始，时间步长为 t，预测接下来的 k 个**值**”

对于机器学习爱好者来说，这就像写“Hello World”一样，因为这个问题在社区中非常有名，通常称为“**预测**”。

机器学习社区开发了许多可以用于预测时间序列下一步值的技术。一些传统方法包括像**ARIMA/SARIMA**这样的算法，或**傅里叶变换分析**，以及其他更复杂的算法，比如**卷积神经网络/循环神经网络**或超著名的“**Transformer**”算法（ChatGPT 中的 T 代表 transformers）。

尽管预测问题是一个众所周知的问题，但讨论**带约束的预测**问题可能并不常见。

让我解释一下我的意思。

你有一个带有一组参数**X**和时间步长**t**的时间序列。

***标准***时间预测…
