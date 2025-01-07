# 通过GitHub Actions实现简单的模型重训练自动化

> 原文：[https://towardsdatascience.com/simple-model-retraining-automation-via-github-actions-b0f61d5c869c?source=collection_archive---------3-----------------------#2024-06-15](https://towardsdatascience.com/simple-model-retraining-automation-via-github-actions-b0f61d5c869c?source=collection_archive---------3-----------------------#2024-06-15)

## 通过GitHub Actions，轻松简化您的建模过程。

[](https://cornelliusyudhawijaya.medium.com/?source=post_page---byline--b0f61d5c869c--------------------------------)[![Cornellius Yudha Wijaya](../Images/438b1179e5dda7aa261b073c71330b74.png)](https://cornelliusyudhawijaya.medium.com/?source=post_page---byline--b0f61d5c869c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b0f61d5c869c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b0f61d5c869c--------------------------------) [Cornellius Yudha Wijaya](https://cornelliusyudhawijaya.medium.com/?source=post_page---byline--b0f61d5c869c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b0f61d5c869c--------------------------------) ·阅读时间：11分钟·2024年6月15日

--

![](../Images/65cdf54fbbcaebff0ed423b7ec619656.png)

由[Praveen Thirumurugan](https://unsplash.com/@praveentcom?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

机器学习模型可以为企业创造巨大的价值。然而，开发它们并不是一次性的活动。相反，它是一个持续的过程，旨在让模型不断提供价值。这就是MLOps的来源。

CI/CD原则与机器学习开发的结合，就是我们所说的MLOps，它旨在通过模型持续提供价值。

机器学习模型能够带来持续利益的一个方法是，当检测到数据漂移时，通过重训练来实现。我们可以通过设置重训练触发器环境来执行模型重训练自动化。

我们可以使用名为[GitHub Actions](https://github.com/features/actions)的GitHub工具来简化重训练过程。这个工具是GitHub为CI/CD平台提供的功能，用于自动化软件开发过程，从GitHub仓库中进行操作。

本文将教我们如何通过GitHub Actions进行模型重训练自动化控制。如何实现呢？让我们一起了解。

# 准备工作

我们将为这个项目进行一次简单的模型开发和自动化演示。整体…
