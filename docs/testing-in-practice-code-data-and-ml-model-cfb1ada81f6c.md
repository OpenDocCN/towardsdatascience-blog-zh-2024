# 实践中的测试：代码、数据与ML模型

> 原文：[https://towardsdatascience.com/testing-in-practice-code-data-and-ml-model-cfb1ada81f6c?source=collection_archive---------10-----------------------#2024-01-12](https://towardsdatascience.com/testing-in-practice-code-data-and-ml-model-cfb1ada81f6c?source=collection_archive---------10-----------------------#2024-01-12)

## MLOps中的测试指南

[](https://medium.com/@Chim-SO?source=post_page---byline--cfb1ada81f6c--------------------------------)[![Chayma Zatout](../Images/341c45f53ddf73dc0851d547cc7cb55a.png)](https://medium.com/@Chim-SO?source=post_page---byline--cfb1ada81f6c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cfb1ada81f6c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cfb1ada81f6c--------------------------------) [Chayma Zatout](https://medium.com/@Chim-SO?source=post_page---byline--cfb1ada81f6c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cfb1ada81f6c--------------------------------) ·12分钟阅读·2024年1月12日

--

![](../Images/3573fa1b38a2fee123c5a26ff2ee8e2e.png)

图片来自[Mohammad Rahmani](https://unsplash.com/@afgprogrammer?utm_source=medium&utm_medium=referral)的[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

如果你想要一个正确且可靠的机器学习模型，且具备良好的性能，那么测试是必须进行的基本实践之一。如果你决定深入了解测试方法，那么你来对地方了。在本文中，我们通过一个实际案例来解释测试的重要性，其中我们将测试应用于机器学习工作流的不同步骤。本文的完整代码库可以在[相关的仓库](https://github.com/Chim-SO/hand-written-digits-classification)中访问。

还不是Medium会员？不用担心！通过这个[*好友链接*](/testing-in-practice-code-data-and-ml-model-cfb1ada81f6c)继续阅读。

**目录：**

· [1\. 介绍](#0232)

· [2\. 项目设置](#1591)

· [3\. 代码测试](#54d1)

∘ [3.1\. 单元测试](#8816)

∘ [3.2\. 集成测试](#e14d)

· [4\. 数据测试](#0e6a)

∘ [4.1\. 数据验证](#6770)

∘ [4.2\. 符合政策要求](#932e)

∘ [4.3\. 特征重要性](#edf3)

· [5\. 模型测试](#91c5)

· [6\. 结论](#91df)

# 1\. 介绍
