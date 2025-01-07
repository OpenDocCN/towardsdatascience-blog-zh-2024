# Proxy SHAP：通过更简单的模型加速可解释性

> 原文：[`towardsdatascience.com/proxy-shap-speed-up-explainability-with-simpler-models-1aab91b79f9f?source=collection_archive---------5-----------------------#2024-09-21`](https://towardsdatascience.com/proxy-shap-speed-up-explainability-with-simpler-models-1aab91b79f9f?source=collection_archive---------5-----------------------#2024-09-21)

## 高效计算 SHAP 值的实用指南

[](https://medium.com/@cerlymarco?source=post_page---byline--1aab91b79f9f--------------------------------)![Marco Cerliani](https://medium.com/@cerlymarco?source=post_page---byline--1aab91b79f9f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1aab91b79f9f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1aab91b79f9f--------------------------------) [Marco Cerliani](https://medium.com/@cerlymarco?source=post_page---byline--1aab91b79f9f--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1aab91b79f9f--------------------------------) ·阅读时长 6 分钟·2024 年 9 月 21 日

--

![](img/ed677e24385458c745abf30861375dc7.png)

图片来自[Joel Muniz](https://unsplash.com/@jmuniz?utm_source=medium&utm_medium=referral)提供，[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

数据科学家喜欢做实验、训练模型，并且深入数据的泥潭。在项目初期，大家的热情是最高的，但当事情变得复杂或耗时过长时，寻找更简单的解决方案成为一种真正的必要。

在某些情况下，商业利益相关者可能要求对底层解决方案逻辑进行更改，或在尝试提高性能并保持预测算法良好的可解释性水平时进行进一步调整/试验。识别可能导致额外复杂性和延迟的代码实现瓶颈，对于及时交付最终产品至关重要。

想象一下，作为一名数据科学家，你的任务是开发一个预测模型。我们轻松地拥有所有所需的资源，不久之后，我们就准备好向商业团队展示我们基于成千上万个特征和数百万条记录构建的精美预测解决方案，这些解决方案达到了令人惊叹的性能。

商业利益相关者对我们的演示感到很兴奋，并且理解这项技术的潜力，但他们提出了一个要求。他们希望知道模型是如何做出决策的。我们可能会认为，这没什么难的……
