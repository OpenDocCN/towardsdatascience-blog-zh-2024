# 早停法：为什么你的机器学习模型停止训练？

> 原文：[https://towardsdatascience.com/early-stopping-why-did-your-machine-learning-model-stop-training-c6b1d64e009e?source=collection_archive---------7-----------------------#2024-05-11](https://towardsdatascience.com/early-stopping-why-did-your-machine-learning-model-stop-training-c6b1d64e009e?source=collection_archive---------7-----------------------#2024-05-11)

## 为什么大多数模型较小，而大型语言模型（LLMs）较大

[](https://harrisonfhoffman.medium.com/?source=post_page---byline--c6b1d64e009e--------------------------------)[![Harrison Hoffman](../Images/ebd5a797314dc9ef3da4c55acc3f0254.png)](https://harrisonfhoffman.medium.com/?source=post_page---byline--c6b1d64e009e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c6b1d64e009e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c6b1d64e009e--------------------------------) [Harrison Hoffman](https://harrisonfhoffman.medium.com/?source=post_page---byline--c6b1d64e009e--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c6b1d64e009e--------------------------------) ·15分钟阅读·2024年5月11日

--

![](../Images/b41ca7537911f65a13f567179aaac1d0.png)

PL**AI**OFFS24\. 图片来源：作者。

在训练监督学习模型时，早停法是一种常用的技术，用于缓解过拟合。早停法涉及在训练过程中监控模型在验证集上的表现，并在模型在这些验证数据上的表现不再提升时停止训练。此技术有助于节省计算时间和资源，同时确保模型不会学习到训练数据中的噪声和无关模式，从而避免降低模型在新数据上的泛化能力。

早停法是机器学习中广泛认可的一项技术，但关于在不同场景下满足早停条件的具体原因讨论并不多。本文从训练数据质量的角度探讨早停法的复杂性。通过分析数据质量如何影响训练停止的时机，我们可以更深入地理解其重要作用。此外，我们还将解释处理结构化数据和非结构化数据在机器学习项目中的根本区别，重点说明这些差异如何影响早停法的应用。

# 前提条件
