# LLMOps — 使用BentoML提供Llama-3模型服务

> 原文：[https://towardsdatascience.com/llmops-serve-a-llama-3-model-with-bentoml-4d580a7a007f?source=collection_archive---------6-----------------------#2024-08-09](https://towardsdatascience.com/llmops-serve-a-llama-3-model-with-bentoml-4d580a7a007f?source=collection_archive---------6-----------------------#2024-08-09)

![](../Images/338cd1cb8fd84b57aa00595d61685ee8.png)

由[Simon Wiedensohler](https://unsplash.com/@simonwiedensohler?utm_source=medium&utm_medium=referral)拍摄，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 快速设置LLM API，使用BentoML和Runpod

[](https://medium.com/@marcellopoliti?source=post_page---byline--4d580a7a007f--------------------------------)[![Marcello Politi](../Images/484e44571bd2e75acfe5fef3146ab3c2.png)](https://medium.com/@marcellopoliti?source=post_page---byline--4d580a7a007f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4d580a7a007f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4d580a7a007f--------------------------------) [Marcello Politi](https://medium.com/@marcellopoliti?source=post_page---byline--4d580a7a007f--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4d580a7a007f--------------------------------) ·6分钟阅读·2024年8月9日

--

# 引言

我经常看到数据科学家对LLM的模型架构、训练技术或数据收集产生兴趣。然而，我注意到，很多时候，在理论层面之外，许多人在将这些模型部署为可供用户使用时遇到困难。

在这个简短的教程中，我将以非常简单的方式展示如何使用[BentoML](https://www.bentoml.com/)提供LLM服务，特别是llama-3模型。

> BentoML是一个端到端的机器学习模型服务解决方案。它帮助数据科学团队开发生产就绪的模型服务端点，在每个阶段都实现DevOps最佳实践和性能优化。

## 我们需要GPU

如你所知，在深度学习中，拥有合适的硬件至关重要。尤其对于像LLM这样的大型模型，这一点更加重要。不幸的是，我没有GPU 😔

这就是为什么我依赖外部提供商，所以我租用他们的机器并在那里工作。为了这篇文章，我选择了[Runpod](https://www.runpod.io/)，因为我了解他们的服务，并且我认为这个价格适合跟随本教程。但如果你有GPU或者想要…
