# 近距离观察 AI 痛点，以及如何（有时）解决它们

> 原文：[https://towardsdatascience.com/a-close-look-at-ai-pain-points-and-how-to-sometimes-resolve-them-10102dd4309d?source=collection_archive---------15-----------------------#2024-09-26](https://towardsdatascience.com/a-close-look-at-ai-pain-points-and-how-to-sometimes-resolve-them-10102dd4309d?source=collection_archive---------15-----------------------#2024-09-26)

[](https://towardsdatascience.medium.com/?source=post_page---byline--10102dd4309d--------------------------------)[![TDS Editors](../Images/4b2d1beaf4f6dcf024ffa6535de3b794.png)](https://towardsdatascience.medium.com/?source=post_page---byline--10102dd4309d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--10102dd4309d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--10102dd4309d--------------------------------) [TDS 编辑](https://towardsdatascience.medium.com/?source=post_page---byline--10102dd4309d--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--10102dd4309d--------------------------------) ·以 [新闻通讯](./newsletter?source=post_page---byline--10102dd4309d--------------------------------) 形式发送 ·阅读时间：3分钟·2024年9月26日

--

> 想要写你在 TDS 上的第一篇文章吗？[我们始终欢迎新作者的投稿](http://bit.ly/write-for-tds)。

在短短几年内，AI驱动的工具已经从（相对）小众的产品，面向具有专业技能的受众，发展到广泛且迅速被采纳的产品——有时是被那些并未完全理解其权衡和局限性的组织所采纳。

这种巨大的转变几乎可以确保出现失误、瓶颈和痛点。无论是个人还是团队，目前都在应对一项新兴技术的复杂地形，这项技术伴随着许多尚未解决的难题。

本周，我们将重点介绍几篇出色的文章，这些文章以清晰且务实的方式解决了这个难题。从处理幻觉问题到为特定使用场景做出正确的产品选择，它们正面应对了AI的一些最大痛点。它们可能不会为每个可能的场景提供完美的解决方案——在某些情况下，解决方案甚至可能还不存在（或许？）——但它们可以帮助你以正确的心态应对自己的挑战。

+   [**为何GenAI是一个数据删除和隐私噩梦**](/why-genai-is-a-data-deletion-and-privacy-nightmare-bd79a3c0ed85) **“**尝试删除已经融入大规模语言模型的训练数据，就像试图在蛋糕烤好后去除糖分一样。”[Cassie Kozyrkov](https://medium.com/u/2fccb851bb5e?source=post_page---user_mention--10102dd4309d--------------------------------)再次回到TDS，深入分析了在基于用户数据训练模型时可能出现的隐私问题，以及在事后才引入保护措施时解决这些问题的困难。

+   [**通过ARTKIT暴露LLM应用中的越狱漏洞**](/exposing-jailbreak-vulnerabilities-in-llm-applications-with-artkit-d2df5f56ece8) 对LLM（大规模语言模型）产品固有的安全和隐私风险的理解日益增加，尤其是那些复杂的“越狱”技术，可以通过一些坚持和耐心绕过开发者设置的任何数据保护措施。[Kenneth Leung](https://medium.com/u/dcd08e36f2d0?source=post_page---user_mention--10102dd4309d--------------------------------)在他最新的文章中展示了这一问题的紧迫性，文章探讨了如何使用开源的ARTKIT框架自动评估LLM安全漏洞。

![](../Images/cd513add78cb928e69f8cace825a0b44.png)

图片由[Paul Bergmeir](https://unsplash.com/@paulbrgmr?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

+   [**在LLM代理框架之间的选择**](/choosing-between-llm-agent-frameworks-69019493b259) AI代理的兴起为自动化和简化繁琐的工作流提供了新的机会，但也提出了关于如何将合适的工具与合适的任务匹配的紧迫问题。[Aparna Dhinakaran](https://medium.com/u/f32f85889f3a?source=post_page---user_mention--10102dd4309d--------------------------------)的详细概述解决了ML（机器学习）产品经理在选择代理框架时面临的最大困境之一：“你是选择长期存在的LangGraph，还是选择新兴的LlamaIndex Workflows？或者你走传统路线，自己编写整个框架？”

+   [**我如何处理AI初创公司中的幻觉问题**](/how-i-deal-with-hallucinations-at-an-ai-startup-9fc4121295cc) **“**想象一下，AI将发票金额误读为100,000美元，而不是1,000美元，导致超额支付了100倍。” 如果基于LLM的聊天机器人幻觉出一个错误的饼干食谱，你最终得到的是不可食用的零食。如果它用错误的信息回应商业查询，你可能会犯下非常昂贵的错误。从依赖较小的模型到利用基础方法，[Tarik Dzekman](https://medium.com/u/752038078741?source=post_page---user_mention--10102dd4309d--------------------------------)提供了避免这种命运的实用见解，这些见解都基于他在文档自动化和信息提取方面的工作。

如果你正在寻找有关其他数据科学和机器学习主题的阅读推荐，别再犹豫了：

+   在他的TDS首次亮相中，[Iqbal Rahmadhan](https://medium.com/u/5b94ed8c2f60?source=post_page---user_mention--10102dd4309d--------------------------------)带来了一个详细且易懂的介绍，帮助你[理解、计算、可视化和解读比值比](/a-deep-dive-into-odds-ratio-7c327b2b1ac7)及其置信区间。

+   什么是语义层，[如何构建一个有效的语义层](/semantic-layer-for-the-people-and-by-the-people-ce9ecbd0a6f6)？[Marina Tosic](https://medium.com/u/e40b4f03cd3e?source=post_page---user_mention--10102dd4309d--------------------------------)的实践指南为刚刚入门的你提供了所有答案。

+   如果你对涉及多语言方法的机器学习工作流感兴趣，千万不要错过[Milan Tamang](https://medium.com/u/141fa70b60b6?source=post_page---user_mention--10102dd4309d--------------------------------)的逐步指南，展示了[如何构建泰语多语言子词分词器](/build-a-tokenizer-for-the-thai-language-from-scratch-0e4ea5f2a8b3)。

+   如果[加速你的深度学习模型训练](/the-mystery-behind-the-pytorch-automatic-mixed-precision-library-d9386e4b787e)对你的当前项目有帮助，[Mengliu Zhao](https://medium.com/u/6db175d93233?source=post_page---user_mention--10102dd4309d--------------------------------)将带领我们深入了解自动混合精度技术的内部工作原理。

+   在最新一期引人入胜的“水冷器闲聊”系列中，[Maria Mouschoutzi, PhD](https://medium.com/u/dce3cb684eae?source=post_page---user_mention--10102dd4309d--------------------------------)通过统计学和一点Python，[揭示了著名的生日悖论](/water-cooler-small-talk-the-birthday-paradox-94ea294502c7)。

+   对于刚接触 NumPy 及其众多强大功能的用户，应该探索[Lee Vaughan](https://medium.com/u/5d604015c08b?source=post_page---user_mention--10102dd4309d--------------------------------)的四部分新手友好系列，介绍了这个重要的 Python 库——[第 1 部分自然是一个很好的起点](/introducing-numpy-part-1-understanding-arrays-3f6fecc97e3d)。

感谢您支持我们作者的工作！正如我们之前提到的，我们非常喜欢发布新作者的文章，因此，如果您最近写了一篇有趣的项目讲解、教程或关于我们核心主题的理论思考，请不要犹豫，[与我们分享](http://bit.ly/write-for-tds)。

直到下一个变量，

TDS 团队
