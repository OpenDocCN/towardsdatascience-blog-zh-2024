# Mac 上的本地 LLM 微调（M1 16GB）

> 原文：[`towardsdatascience.com/local-llm-fine-tuning-on-mac-m1-16gb-f59f4f598be7?source=collection_archive---------2-----------------------#2024-08-01`](https://towardsdatascience.com/local-llm-fine-tuning-on-mac-m1-16gb-f59f4f598be7?source=collection_archive---------2-----------------------#2024-08-01)

## 初学者友好的 Python 代码演示（特邀：MLX）

[](https://shawhin.medium.com/?source=post_page---byline--f59f4f598be7--------------------------------)![Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--f59f4f598be7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f59f4f598be7--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f59f4f598be7--------------------------------) [Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--f59f4f598be7--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f59f4f598be7--------------------------------) ·8 分钟阅读·2024 年 8 月 1 日

--

本文是关于使用大型语言模型（LLM）实践的[系列文章](https://shawhin.medium.com/list/large-language-models-llms-8e009ae3054c)的一部分。在上一篇文章中，我展示了如何在 Google Colab 上使用一块（免费的）GPU 微调 LLM。虽然那个例子（以及许多其他例子）可以在 Nvidia 硬件上轻松运行，但它们无法轻松适配 M 系列 Mac。在本文中，我将介绍如何在 Mac 上本地微调 LLM 的一种简单方法。

![](img/ff42af007f050edf17f7fa56426c6535.png)

图片由[Myron Mott](https://unsplash.com/@s2killa?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

随着开源大型语言模型（LLM）和高效微调方法的兴起，构建定制的机器学习解决方案变得前所未有的容易。现在，任何拥有**一块 GPU 的人都可以在本地机器上微调 LLM**。

然而，由于苹果的 M 系列芯片，Mac 用户在这一趋势中基本被排除在外。这些芯片采用了统一内存架构，这使得不再需要 GPU。因此，许多（以 GPU 为中心的）开源工具无法兼容（或无法充分利用）现代 Mac 的计算能力，用于运行和训练 LLM。

我差点**放弃**了在本地训练 LLM 的梦想，直到我发现了 MLX Python 库。
