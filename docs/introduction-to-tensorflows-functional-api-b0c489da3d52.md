# TensorFlow 的功能性 API 介绍

> 原文：[`towardsdatascience.com/introduction-to-tensorflows-functional-api-b0c489da3d52?source=collection_archive---------4-----------------------#2024-12-18`](https://towardsdatascience.com/introduction-to-tensorflows-functional-api-b0c489da3d52?source=collection_archive---------4-----------------------#2024-12-18)

## 学习功能性 API 是什么，以及如何使用它构建复杂的 Keras 模型

[](https://medium.com/@JavierMtz5?source=post_page---byline--b0c489da3d52--------------------------------)![Javier Martínez Ojeda](https://medium.com/@JavierMtz5?source=post_page---byline--b0c489da3d52--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b0c489da3d52--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b0c489da3d52--------------------------------) [Javier Martínez Ojeda](https://medium.com/@JavierMtz5?source=post_page---byline--b0c489da3d52--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b0c489da3d52--------------------------------) ·阅读时间 6 分钟·2024 年 12 月 18 日

--

![](img/eaad0664a8c711c01d10842808f78857.png)

图片来自[Hunter Harritt](https://unsplash.com/@hharritt?utm_source=medium&utm_medium=referral)于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

**TensorFlow**的**顺序 API**帮助用户将各层依次堆叠，轻松创建线性模型，其中每一层的输入始终是上一层的输出。但当你的模型需要处理多个输入或输出、共享层或非线性连接时，会发生什么呢？在这些场景中，**TensorFlow 的功能性 API**可以构建更高级、更灵活、更可定制的模型，使用户能够轻松设计上述复杂架构。

本文将解释：

+   什么是功能性 API

+   如何使用功能性 API 构建一个简单模型

# 什么是 TensorFlow 的功能性 API？

TensorFlow 的**功能性 API**是一种创建模型的方式，其中各层像网络图一样连接，而不仅仅是像**顺序 API**那样线性堆叠。可以想象使用乐高积木：顺序 API 只允许我们将积木块一个个堆叠，而功能性 API 则允许我们构建桥梁、塔楼和连接不同区域的小径。
