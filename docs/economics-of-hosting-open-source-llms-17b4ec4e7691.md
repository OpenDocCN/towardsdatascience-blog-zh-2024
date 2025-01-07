# 托管开源LLMs的经济学

> 原文：[https://towardsdatascience.com/economics-of-hosting-open-source-llms-17b4ec4e7691?source=collection_archive---------0-----------------------#2024-11-12](https://towardsdatascience.com/economics-of-hosting-open-source-llms-17b4ec4e7691?source=collection_archive---------0-----------------------#2024-11-12)

## 大型语言模型在生产中的应用

## 利用各种部署选项

[](https://medium.com/@ilsilfverskiold?source=post_page---byline--17b4ec4e7691--------------------------------)[![Ida Silfverskiöld](../Images/a2c0850bc0198688f70a5eca858cf8b5.png)](https://medium.com/@ilsilfverskiold?source=post_page---byline--17b4ec4e7691--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--17b4ec4e7691--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--17b4ec4e7691--------------------------------) [Ida Silfverskiöld](https://medium.com/@ilsilfverskiold?source=post_page---byline--17b4ec4e7691--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--17b4ec4e7691--------------------------------) ·阅读时间19分钟·2024年11月12日

--

![](../Images/451f57a8bda4e9b8e4b0f293e3d83b43.png)

GPU与CPU上的总处理时间 — 非比例缩放* | 图片由作者提供

*如果你不是会员但想阅读本文，请查看这个朋友链接* [*这里。*](https://medium.com/@ilsilfverskiold/17b4ec4e7691?sk=2649166d7d7a839885bb3fff2a3922bd)

如果你已经在尝试不同规模的开源模型，你可能会问自己：部署它们最有效的方式是什么？

**按需**和**无服务器提供商**之间的定价差异是什么？当有LLM服务平台时，真的值得与像AWS这样的玩家打交道吗？

我决定深入探讨这个话题，将AWS等云服务商与Modal、BentoML、Replicate、Hugging Face Endpoints和Beam等新兴替代方案进行比较。

我们将研究处理时间、冷启动延迟、CPU、内存和GPU成本等指标，以了解哪种方式最有效且经济。我们还将涵盖一些软性指标，如部署的难易程度、开发者体验和社区支持。

![](../Images/d6a4df8a4c00c6bf8ec5c79689b6ec63.png)

我们将查看的一些指标 | 图片由作者提供

我们将探讨一些使用案例，比如在CPU上部署**较小的模型**与在GPU上运行**70亿到80亿参数的模型**的区别。
