# Keras 3.0 教程：端到端深度学习项目指南

> 原文：[https://towardsdatascience.com/keras-3-0-tutorial-end-to-end-deep-learning-project-guide-3552187e3ff5?source=collection_archive---------1-----------------------#2024-05-18](https://towardsdatascience.com/keras-3-0-tutorial-end-to-end-deep-learning-project-guide-3552187e3ff5?source=collection_archive---------1-----------------------#2024-05-18)

## 从头实现一个编码器-解码器递归网络

[](https://qtalen.medium.com/?source=post_page---byline--3552187e3ff5--------------------------------)[![Peng Qian](../Images/9ce9aeb381ec6b017c1ee5d4714937e2.png)](https://qtalen.medium.com/?source=post_page---byline--3552187e3ff5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3552187e3ff5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3552187e3ff5--------------------------------) [Peng Qian](https://qtalen.medium.com/?source=post_page---byline--3552187e3ff5--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3552187e3ff5--------------------------------) ·12分钟阅读·2024年5月18日

--

![](../Images/fe4279573dca9dad3407a1ca9b80136c.png)

Keras 3.0 教程：端到端深度学习项目指南。图片由作者提供

# 介绍

尽管我在一段时间前就开始使用 Pytorch，但我仍然怀念 Keras 简洁的代码风格，以及那段你可以用几行代码就实现一个神经网络模型的美好时光。

所以，当 Keras 在去年 11 月宣布除了 TensorFlow 之外，它现在还支持 Pytorch 和 Jax 作为后端时，我感到非常兴奋！

然而，事情并不完美：由于 Keras 3.0 是最近发布的，相关的教程和文档还没有赶上，我在代码迁移过程中遇到了一些问题。

幸运的是，在一些努力之后，我现在可以顺利使用 3.0 版本进行各种端到端的模型开发。

在这篇文章中，我将分享一些使用 Keras 3.0 的实际经验，帮助你避免一些弯路。我将使用一个典型的编码器-解码器递归神经网络作为例子，解释如何使用 Keras 3.0 的子类化 API 从零开始完成一个端到端的项目，并讨论在使用 Pytorch 作为后端时需要考虑的细节。

让我们开始吧。

# 框架安装与环境设置
