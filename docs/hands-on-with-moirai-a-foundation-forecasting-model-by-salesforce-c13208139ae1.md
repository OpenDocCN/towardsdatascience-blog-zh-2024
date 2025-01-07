# 实战操作 Moirai：Salesforce 的基础预测模型

> 原文：[https://towardsdatascience.com/hands-on-with-moirai-a-foundation-forecasting-model-by-salesforce-c13208139ae1?source=collection_archive---------4-----------------------#2024-08-20](https://towardsdatascience.com/hands-on-with-moirai-a-foundation-forecasting-model-by-salesforce-c13208139ae1?source=collection_archive---------4-----------------------#2024-08-20)

## 了解 Moirai 的架构及其内部工作原理，并将其应用于使用 Python 的预测项目

[](https://medium.com/@marcopeixeiro?source=post_page---byline--c13208139ae1--------------------------------)[![Marco Peixeiro](../Images/7cf0a81d87281d35ff47f51e3026a3e9.png)](https://medium.com/@marcopeixeiro?source=post_page---byline--c13208139ae1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c13208139ae1--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c13208139ae1--------------------------------) [Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--c13208139ae1--------------------------------)

·发表于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--c13208139ae1--------------------------------) ·阅读时长14分钟·2024年8月20日

--

![](../Images/5b4055670e1edab3576e9c6710ed80bb.png)

图片由[Shreyas Bhosale](https://unsplash.com/@shreyasrock2?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我们已经进入了一个大规模基础模型普及的时代。基础模型已经在许多领域引发了革命，如计算机视觉和自然语言处理，出现了能够生成文本、图像和视频的模型和应用。

时间序列预测领域同样未能抵挡这一潮流，许多基础模型开始应用于预测。这标志着一个重要的范式转变，因为我们现在可以生成零-shot（零样本）时间序列数据预测，避免了训练数据特定模型的成本和开发时间。

2023年10月，[TimeGPT-1](/timegpt-the-first-foundation-model-for-time-series-forecasting-bf0a75e63b3a)发布，标志着首批基础预测模型的诞生。随后，在2024年2月，[Lag-Llama](/lag-llama-open-source-foundation-model-for-time-series-forecasting-9afdfaf2bd7c)发布，并在2024年3月紧接着发布了[Chronos](/chronos-the-latest-time-series-forecasting-foundation-model-by-amazon-2687d641705a)。

2024年5月，发布了一种新的开源基础预测模型：Moirai。在论文[统一训练的通用时间序列预测变换器](https://arxiv.org/pdf/2402.02592)中，Salesforce的研究人员提出了一种能够进行概率性零-shot预测的基础模型，同时还支持外生特征。
