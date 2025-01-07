# 策略梯度：RLHF的基础

> 原文：[https://towardsdatascience.com/policy-gradients-the-foundation-of-rlhf-337346beef40?source=collection_archive---------7-----------------------#2024-02-06](https://towardsdatascience.com/policy-gradients-the-foundation-of-rlhf-337346beef40?source=collection_archive---------7-----------------------#2024-02-06)

## 理解策略优化及其在强化学习中的应用

[](https://wolfecameron.medium.com/?source=post_page---byline--337346beef40--------------------------------)[![Cameron R. Wolfe, 博士](../Images/52bb88d7cf1105501be2fae5ccbe7a03.png)](https://wolfecameron.medium.com/?source=post_page---byline--337346beef40--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--337346beef40--------------------------------)[![数据科学前沿](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--337346beef40--------------------------------) [Cameron R. Wolfe, 博士](https://wolfecameron.medium.com/?source=post_page---byline--337346beef40--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--337346beef40--------------------------------) ·15分钟阅读·2024年2月6日

--

![](../Images/aab3d055f178ffac6b75ff4d80f331ad.png)

（照片由[WrongTog](https://unsplash.com/@wrongtog?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)拍摄，来源于[Unsplash](https://unsplash.com/photos/blue-and-red-light-streaks-nYh3nHalEMA?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)）

尽管强化学习（RL）在多种应用中非常有用，但它在大规模语言模型（LLMs）对齐过程中的作用至关重要，特别是在[强化学习与人类反馈（RLHF）](https://aman.ai/primers/ai/RLHF/)中的应用。不幸的是，RL在AI社区中并不广为人知。也就是说，许多从业者（包括我自己）更熟悉监督学习技术，这导致了对使用RL的潜在偏见，尽管它具有巨大的实用性。在这一系列概述中，我们的目标是通过全面回顾RL，从基本思想入手，逐步过渡到现代算法，如[近端策略优化（PPO）](https://openai.com/research/openai-baselines-ppo) [7]，这些算法在RLHF中被广泛使用，从而减少这种偏见。

![](../Images/40b82c39eff8ef3118d07ced407be0ae.png)

现代强化学习算法的分类（来自[5]）

**本概述。** 如上所示，存在两种类型的无模型强化学习（RL）算法：Q学习和策略优化。之前，我们学习了Q学习、强化学习的基础知识，以及这些思想如何可以推广到语言模型微调。在本概述中，我们将概述策略优化和策略梯度这两个在实践中广泛应用的思想……
