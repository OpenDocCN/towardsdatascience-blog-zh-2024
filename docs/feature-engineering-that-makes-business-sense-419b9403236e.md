# 具有商业意义的特征工程

> 原文：[`towardsdatascience.com/feature-engineering-that-makes-business-sense-419b9403236e?source=collection_archive---------2-----------------------#2024-04-28`](https://towardsdatascience.com/feature-engineering-that-makes-business-sense-419b9403236e?source=collection_archive---------2-----------------------#2024-04-28)

## 作者概述了三种方法，通过这些方法，你可以扩展机器学习的特征集，加入能够解释行为并最大化预测能力的特征。

[](https://medium.com/@guillaume.colley?source=post_page---byline--419b9403236e--------------------------------)![Guillaume Colley](https://medium.com/@guillaume.colley?source=post_page---byline--419b9403236e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--419b9403236e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--419b9403236e--------------------------------) [Guillaume Colley](https://medium.com/@guillaume.colley?source=post_page---byline--419b9403236e--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--419b9403236e--------------------------------) ·阅读时间：6 分钟·2024 年 4 月 28 日

--

![](img/1b2289f4df73ff8bc7be0b5c5f999274.png)

面向商业的特征工程 — 图片来自 DALL-E3

当涉及到商业应用中的机器学习时，你很可能需要与业务利益相关者合作，实施你所建立的模型：你正在为他们构建一个能够改善其流程或定位的工具。

除非你所帮助的业务领域在分析方面已经非常先进，并且对机器学习有深入理解（这种情况很少见），否则说服利益相关者相信你的模型是合理的至关重要。

然而，深度学习和基于树的模型（如 XGBoost 或 Random Forest 模型）通常是一个黑盒。因此，向利益相关者展示你的模型是如何工作的——或者更确切地说，是什么影响了模型——是关键。像 [SHAP 图](https://medium.com/@shahooda637/all-you-need-to-know-about-shap-for-explainable-ai-8ad35a05e6ec) 这样的工具非常有用，帮助你理解哪些特征具有预测能力，以及它们的方向性（特征值较低/较高 = 对预测的正面或负面影响）。但你如何决定应该集中精力去工程哪些特征呢？

你可以添加并相乘所有可能的特征组合，然后将其输入…
