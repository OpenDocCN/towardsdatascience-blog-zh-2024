# TFT：一个可解释的Transformer

> 原文：[https://towardsdatascience.com/tft-an-interpretable-transformer-70147bcf6212?source=collection_archive---------5-----------------------#2024-01-05](https://towardsdatascience.com/tft-an-interpretable-transformer-70147bcf6212?source=collection_archive---------5-----------------------#2024-01-05)

## 深入探讨TFT及其使用Darts实现的过程，以及如何解释一个Transformer

[](https://medium.com/@rjguedes?source=post_page---byline--70147bcf6212--------------------------------)[![Rafael Guedes](../Images/b3d000b3bce0113d2b2727e84db04870.png)](https://medium.com/@rjguedes?source=post_page---byline--70147bcf6212--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--70147bcf6212--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--70147bcf6212--------------------------------) [Rafael Guedes](https://medium.com/@rjguedes?source=post_page---byline--70147bcf6212--------------------------------)

·发布在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--70147bcf6212--------------------------------) ·12分钟阅读·2024年1月5日

--

# 介绍

世界上每个公司都需要预测来规划他们的运营，无论他们所处的行业是什么。公司中有多个预测应用场景需要解决，比如年度销售规划、每月根据语言规划客服代理的联系量、根据SKU销售计划生产和/或采购等等。

尽管有不同的应用场景，但它们都来自同一个需求：**可解释性！** 如果你曾经为某个利益相关者部署过预测模型，你可能遇到过这样的问题：***“为什么模型会做出这样的预测？”***

在这篇文章中，我探讨了TFT，一个用于时间序列预测的可解释Transformer。我还提供了一个逐步实现TFT的例子，用于使用Darts（一个Python的预测库）预测沃尔玛数据集中的每周销售。最后，我展示了如何解释模型及其在沃尔玛数据集中16周预测期的表现。

![](../Images/e2cdadb6bc07d21376fc27e06c2ebe9b.png)

图1：可解释的AI（图像由作者使用DALL-E生成）

一如既往，代码可以在[Github](https://github.com/rjguedes8/tft)上找到。

# TFT：时间融合Transformer

## 它是什么？
