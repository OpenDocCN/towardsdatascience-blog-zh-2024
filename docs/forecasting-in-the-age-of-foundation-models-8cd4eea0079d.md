# 在基础模型时代的预测

> 原文：[https://towardsdatascience.com/forecasting-in-the-age-of-foundation-models-8cd4eea0079d?source=collection_archive---------1-----------------------#2024-07-20](https://towardsdatascience.com/forecasting-in-the-age-of-foundation-models-8cd4eea0079d?source=collection_archive---------1-----------------------#2024-07-20)

## 将Lag-Llama与XGBoost进行基准测试

[](https://medium.com/@acorralescano?source=post_page---byline--8cd4eea0079d--------------------------------)[![Alvaro Corrales Cano](../Images/988209a9b396fb3b0b287880eb9bb05a.png)](https://medium.com/@acorralescano?source=post_page---byline--8cd4eea0079d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--8cd4eea0079d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--8cd4eea0079d--------------------------------) [Alvaro Corrales Cano](https://medium.com/@acorralescano?source=post_page---byline--8cd4eea0079d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--8cd4eea0079d--------------------------------) ·14分钟阅读·2024年7月20日

--

![](../Images/724f0f935e21123b18414852a83a416a.png)

Ribadesella附近的悬崖。图片由 [Enric Domas](https://unsplash.com/@henrymd?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来源于 [Unsplash](https://unsplash.com/photos/body-of-water-near-mountain-during-daytime-FNJfLCMO3Bk?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

在写作时，Hugging Face上有20个标记为“时间序列”的模型。虽然数量不算多（“text-generation-inference”标签下有125,950个结果），但使用基础模型进行时间序列预测是一个足够有趣的领域，足以让像Amazon、IBM和Salesforce这样的大公司开发出自己的模型：分别是Chronos、TinyTimeMixer和Moirai。在写作时，Hugging Face上最受欢迎的模型之一是 [Lag-Llama](https://huggingface.co/time-series-foundation-models/Lag-Llama)，它是一个单变量概率模型。该模型由Kashif Rasul、Arjun Ashok及其合著者 [1] 开发，并于2024年2月开源。模型的作者声称它在多个领域的不同数据集上具有“强大的零-shot泛化能力”。一旦针对特定任务进行微调，他们还声称它是同类模型中最好的通用模型。大言不惭！

在这篇博客中，我展示了自己在微调Lag-Llama方面的经验，并将其与更经典的机器学习方法进行对比测试。特别地，我将其与一个旨在处理单变量时间序列数据的XGBoost模型进行了基准测试。像XGBoost这样的梯度提升算法被广泛认为是“经典”机器学习的代表（与深度学习相对），并且已被证明在…
