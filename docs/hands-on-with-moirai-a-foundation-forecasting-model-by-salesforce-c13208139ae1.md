# 实战操作 Moirai：Salesforce 的基础预测模型

> 原文：[`towardsdatascience.com/hands-on-with-moirai-a-foundation-forecasting-model-by-salesforce-c13208139ae1?source=collection_archive---------4-----------------------#2024-08-20`](https://towardsdatascience.com/hands-on-with-moirai-a-foundation-forecasting-model-by-salesforce-c13208139ae1?source=collection_archive---------4-----------------------#2024-08-20)

## 了解 Moirai 的架构及其内部工作原理，并将其应用于使用 Python 的预测项目

[](https://medium.com/@marcopeixeiro?source=post_page---byline--c13208139ae1--------------------------------)![Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--c13208139ae1--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c13208139ae1--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c13208139ae1--------------------------------) [Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--c13208139ae1--------------------------------)

·发表于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--c13208139ae1--------------------------------) ·阅读时长 14 分钟·2024 年 8 月 20 日

--

![](img/5b4055670e1edab3576e9c6710ed80bb.png)

图片由[Shreyas Bhosale](https://unsplash.com/@shreyasrock2?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我们已经进入了一个大规模基础模型普及的时代。基础模型已经在许多领域引发了革命，如计算机视觉和自然语言处理，出现了能够生成文本、图像和视频的模型和应用。

时间序列预测领域同样未能抵挡这一潮流，许多基础模型开始应用于预测。这标志着一个重要的范式转变，因为我们现在可以生成零-shot（零样本）时间序列数据预测，避免了训练数据特定模型的成本和开发时间。

2023 年 10 月，TimeGPT-1 发布，标志着首批基础预测模型的诞生。随后，在 2024 年 2 月，Lag-Llama 发布，并在 2024 年 3 月紧接着发布了 Chronos。

2024 年 5 月，发布了一种新的开源基础预测模型：Moirai。在论文[统一训练的通用时间序列预测变换器](https://arxiv.org/pdf/2402.02592)中，Salesforce 的研究人员提出了一种能够进行概率性零-shot 预测的基础模型，同时还支持外生特征。
