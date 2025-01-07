# AI 实践：如何选择和部署正确的策略

> 原文：[https://towardsdatascience.com/ai-in-practice-how-to-choose-and-deploy-the-right-strategy-bcda1ed5c1c3?source=collection_archive---------5-----------------------#2024-10-24](https://towardsdatascience.com/ai-in-practice-how-to-choose-and-deploy-the-right-strategy-bcda1ed5c1c3?source=collection_archive---------5-----------------------#2024-10-24)

[](https://towardsdatascience.medium.com/?source=post_page---byline--bcda1ed5c1c3--------------------------------)[![TDS 编辑](../Images/4b2d1beaf4f6dcf024ffa6535de3b794.png)](https://towardsdatascience.medium.com/?source=post_page---byline--bcda1ed5c1c3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bcda1ed5c1c3--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bcda1ed5c1c3--------------------------------) [TDS 编辑](https://towardsdatascience.medium.com/?source=post_page---byline--bcda1ed5c1c3--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bcda1ed5c1c3--------------------------------) ·通过 [通讯]( /newsletter?source=post_page---byline--bcda1ed5c1c3--------------------------------) 发送 ·3 分钟阅读·2024年10月24日

--

> 想写你的第一篇 TDS 文章吗？[我们总是欢迎新作者的贡献](http://bit.ly/write-for-tds)。

如今，几乎已经成为共识，大多数机器学习项目无法进入生产环境，而那些成功进入生产的，也往往未能兑现其承诺。

我们应该始终对这些类似的广泛声明保持一定的怀疑，因为准确的统计数据很难收集（和解释），而且传播这些数据的组织往往有动机说服从业者相信*他们*的解决方案是解决所有AI整合挑战的关键。尽管如此，很难忽视如此多来自我们社区不同角落的声音——他们承认，收获这一新兴技术的好处比表面上看起来要难得多。

我们每周的亮点聚焦于选择、采用和最大化利用AI驱动的产品与工作流程的实际方面。对于将有前景但复杂的工具整合到业务中的问题，永远没有一种“适用于所有人的”解决方案，但我们认为，探讨这些文章可以以更有用和务实的方式框定讨论。让我们开始吧。

+   [**利用人工智能打造竞争优势**](/carving-out-your-competitive-advantage-with-ai-a4babb931076)企业*到底*能通过使用人工智能获得哪些好处？[Dr. Janna Lipenkova](https://medium.com/u/f215f8e427a2?source=post_page---user_mention--bcda1ed5c1c3--------------------------------)扩展了你可以采用的思维模型，帮助你做出更智能的设计和产品决策，这将使你能够为你的组织找到人工智能的“甜点”——超越自动化，开辟更多创造力和创新的空间。

+   [**将多模态数据集成到大型语言模型中**](/integrating-multimodal-data-into-a-large-language-model-d1965b8ab00c)[Umair Ali Khan](https://medium.com/u/5ab614522a5a?source=post_page---user_mention--bcda1ed5c1c3--------------------------------) 提供了一个详细的实操介绍，讲解了一种前沿方法，该方法基于最近的上下文检索工作，不仅可以将文本数据纳入你的RAG管道，还可以包括视觉媒体。从收据到图表和表格，机器学习工作流现在可以通过使用更丰富的多模态数据变得更加强大。

![](../Images/b01a71e310dd158ef81742a00e8a48ad.png)

图片由[Stephen Harlan](https://unsplash.com/@gogostevie?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

+   [**如何选择最佳的机器学习部署策略：云端与边缘**](/how-to-choose-the-best-ml-deployment-strategy-cloud-vs-edge-7b62d9db9b20)“随着机器学习的普及，对于可扩展且高效的部署方法需求日益增长，但具体细节往往仍不明确。”[Vincent Vandenbussche](https://medium.com/u/6c53f1364ba9?source=post_page---user_mention--bcda1ed5c1c3--------------------------------)耐心地引导我们了解机器学习工程师在为特定项目和用例选择最佳选项时需要考虑的各种因素。

+   [**Nvidia最新多模态LLM家族的详细解读**](/a-walkthrough-of-nvidias-latest-multi-modal-llm-family-fdc067b59596)紧跟人工智能领域的新发展无疑非常重要，但新模型和工具迅速涌现的节奏往往使得繁忙的数据专业人士难以跟上。[Mengliu Zhao](https://medium.com/u/6db175d93233?source=post_page---user_mention--bcda1ed5c1c3--------------------------------)最近的总结为我们提供了NVIDIA发布的全新多模态LLM套件的有用概览，并将它们的性能与其他模型（包括商业和开源）进行了对比。

为什么不拓展一下本周的其他话题呢？我们有一些精彩的文章推荐给你：

+   [从基础开始学习贝叶斯建模](/an-extensive-starters-guide-for-causal-discovery-using-bayesian-modeling-cfd40839a308) —— [Erdogan Taskesen](https://medium.com/u/4e636e2ef813?source=post_page---user_mention--bcda1ed5c1c3--------------------------------)的深入分析既全面又易于理解。

+   视觉编码器如何“看”？[Ruth Crasto](https://medium.com/u/f11d5eb7e93d?source=post_page---user_mention--bcda1ed5c1c3--------------------------------)带领我们探讨[零样本定位的内部工作原理](/zero-shot-localization-with-clip-style-encoders-2ac3cea172a8)与CLIP风格编码器。

+   在她的最新一轮实验中，[Yennie Jun](https://medium.com/u/12ca1ab81192?source=post_page---user_mention--bcda1ed5c1c3--------------------------------)将[LLM相互对抗，看看它们在玩桌面游戏Codenames时的表现](/llm-vs-llm-codenames-tournament-f8170dd1c8fb)。

+   对旧有假设施加关键性压力总是好的——正如[Conor O'Sullivan](https://medium.com/u/4ae48256fb37?source=post_page---user_mention--bcda1ed5c1c3--------------------------------)在一篇文章中所做的那样，[剖析了机器学习中的准确性与可解释性权衡](/the-accuracy-vs-interpretability-trade-off-is-a-lie-0bf89c84f5b3)。

+   对自动微分不熟悉？[Ebrahim Pichka](https://medium.com/u/cf08d1e97a71?source=post_page---user_mention--bcda1ed5c1c3--------------------------------)的入门文章[介绍了AutoDiff的机制](/automatic-differentiation-autodiff-a-brief-intro-with-examples-3f3d257ffe3b)，并探讨了其基础数学和常见的实现策略。

+   对任何想要扩展数据可视化工具包的人，[Isha Garg](https://medium.com/u/48cdabd739e9?source=post_page---user_mention--bcda1ed5c1c3--------------------------------)的最新教程概述了[在Tableau中创建流线型辐射图的逐步过程](/all-you-need-to-know-to-build-radial-charts-in-tableau-686efc218c9c)。

+   我们如何[准确评估大规模社会或经济实验的影响](/dont-do-laundry-today-it-will-be-cheaper-tomorrow-84a6baa4e626)？[Alvaro Corrales Cano](https://medium.com/u/639157103f84?source=post_page---user_mention--bcda1ed5c1c3--------------------------------)的深刻文章将因果推断框架应用于2013年的低碳伦敦项目。

+   地理空间数据爱好者，这篇文章是为你们准备的：[Helen McKenzie](https://medium.com/u/1701772dbb74?source=post_page---user_mention--bcda1ed5c1c3--------------------------------)的简洁指南展示了[如何使用免费的GIS软件和数据进行可视性分析](/running-visibility-analysis-in-qgis-d8fbdd8b02c5)，并说明了如何使用结果创建有效的可视化。

感谢您支持我们作者的工作！正如我们之前提到的，我们非常乐意发布新作者的文章，因此，如果您最近写了一篇有趣的项目教程、教程或关于我们核心主题的理论思考，千万不要犹豫，[与我们分享](http://bit.ly/write-for-tds)。

直到下一个《Variable》，

TDS 团队
