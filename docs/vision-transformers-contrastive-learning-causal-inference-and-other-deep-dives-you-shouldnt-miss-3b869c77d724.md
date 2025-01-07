# 视觉变换器、对比学习、因果推断以及其他你不容错过的深度解析

> 原文：[https://towardsdatascience.com/vision-transformers-contrastive-learning-causal-inference-and-other-deep-dives-you-shouldnt-miss-3b869c77d724?source=collection_archive---------6-----------------------#2024-08-15](https://towardsdatascience.com/vision-transformers-contrastive-learning-causal-inference-and-other-deep-dives-you-shouldnt-miss-3b869c77d724?source=collection_archive---------6-----------------------#2024-08-15)

[](https://towardsdatascience.medium.com/?source=post_page---byline--3b869c77d724--------------------------------)[![TDS 编辑团队](../Images/4b2d1beaf4f6dcf024ffa6535de3b794.png)](https://towardsdatascience.medium.com/?source=post_page---byline--3b869c77d724--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3b869c77d724--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3b869c77d724--------------------------------) [TDS 编辑团队](https://towardsdatascience.medium.com/?source=post_page---byline--3b869c77d724--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3b869c77d724--------------------------------) ·通过 [新闻通讯](/newsletter?source=post_page---byline--3b869c77d724--------------------------------) 发送 ·3分钟阅读·2024年8月15日

--

> 想写你的第一篇TDS文章吗？[我们总是欢迎新作者的投稿](http://bit.ly/write-for-tds)。

随着我们许多人进入夏季的最后阶段，为什么不趁着通常较为平静的几周，抓住机会在通常繁忙的九月来临前，探索数据科学和机器学习的新话题呢？

为了帮助我们读者中所有学习者和技能提升者，本周我们推出了《变量》的特别版，专门介绍我们最近的最佳深度解析文章（以及其他需要比平时更多时间和注意力的文章）。它们的阅读时间可能较长，但在涵盖各自主题时，能够以细腻的视角、周到的方式，并且注重实际应用，做到非常出色。希望你能喜欢我们的精选文章。

+   [**对比学习实用指南**](/a-practical-guide-to-contrastive-learning-26e912c0362f) 对比学习适用于学习没有显式标签的基础数据表示，具有众多现实世界的应用场景；[孟柳·赵](https://medium.com/u/6db175d93233?source=post_page---user_mention--3b869c77d724--------------------------------)将通过FashionMNIST数据集的示例，引导我们完成构建SimSiam模型的过程。

+   [**论文讲解：Vision Transformer (ViT)**](/paper-walkthrough-vision-transformer-vit-c5dcf76f1a7a)我们总是乐于深入分析一篇扎实全面的论文，尤其是当它涵盖了像Vision Transformer这样具有突破性的概念时。如果你对这一主题不太了解，或者想要扩展现有的ViT知识，不要错过[Muhammad Ardi](https://medium.com/u/9801a58700ac?source=post_page---user_mention--3b869c77d724--------------------------------)的首篇TDS文章。

+   [**通过BatchNorm加速Vision Transformer**](/speeding-up-the-vision-transformer-with-batch-normalization-d37f13f20ae7)让我们再多停留在Vision Transformer上稍作探讨：如果你已经熟悉它，但希望能更高效、流畅地完成工作流程，[Anindya Dey, PhD](https://medium.com/u/6527aecbd3c5?source=post_page---user_mention--3b869c77d724--------------------------------)提供了一份全面指南，讲解如何将批量归一化（Batch Normalization）集成到仅编码器的Transformer架构中，从而减少训练和推理时间。

+   [**通过生成式AI提升电子商务——第一部分**](/enhancing-e-commerce-with-generative-ai-part-1-9e402fb30e7b)最近发布的AI工具的某些预期效益仍待观察。[Mina Ghashami](https://medium.com/u/c99ed9ed7b9a?source=post_page---user_mention--3b869c77d724--------------------------------)呈现了一个新系列，专注于那些生成式AI应用已开始产生实际影响的使用场景，首先探讨了电子商务平台最常见（且对业务至关重要）的任务之一：产品推荐。

![](../Images/3825c247c2abb7560bc8364e424aca21.png)

由[Nellie Adamyan](https://unsplash.com/@nellie_adamyan?utm_source=medium&utm_medium=referral)拍摄，图片来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

+   [**使用Python进行因果推断：倾向得分匹配指南**](/causal-inference-with-python-a-guide-to-propensity-score-matching-b3470080c84f)将理论与实践结合，[Lukasz Szubelak](https://medium.com/u/c1c5a4c55d65?source=post_page---user_mention--3b869c77d724--------------------------------)邀请我们深入探索因果推断的方方面面，他耐心的深入分析聚焦于倾向得分匹配（Propensity Score Matching），这是一种在非随机化设置中估计治疗效果的强大技术。

+   [**ChatGPT 与 Claude 与 Gemini 在数据分析中的对比（第一部分）**](/chatgpt-vs-claude-vs-gemini-for-data-analysis-part-1-821086810318)机器学习从业者在选择使用哪款基于 LLM（大语言模型）驱动的产品时，面临越来越困难的抉择。[Yu Dong](https://medium.com/u/5462c48cfc57?source=post_page---user_mention--3b869c77d724--------------------------------)的这篇新系列旨在通过对三大主流产品（ChatGPT、Claude 和 Gemini）在核心数据分析任务中的表现进行比较，帮助理清这个偶尔显得混乱的生态系统——在这个案例中，重点是编写 SQL 查询。

+   [**遗漏变量偏差**](/omitted-variable-bias-7a23405b6c32)阅读[Sachin Date](https://medium.com/u/b75b5b1730f3?source=post_page---user_mention--3b869c77d724--------------------------------)的数学和统计学解释总是我们的一大亮点——而他最新的文章，关于“回归研究中最常见且容易忽视的偏差之一”，也不例外。我们邀请你深入探索他对遗漏变量偏差的深度分析，文中还概述了几种分析和估算其影响的方法。

感谢你支持我们作者的工作！我们喜欢发布新作者的文章，因此如果你最近写了一篇有趣的项目教程、教程或关于我们核心主题的理论思考，别犹豫，[与我们分享](http://bit.ly/write-for-tds)。

直到下一个变量，

TDS 团队
