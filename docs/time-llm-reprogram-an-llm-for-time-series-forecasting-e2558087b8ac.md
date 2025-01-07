# Time-LLM：为时间序列预测重编程LLM

> 原文：[https://towardsdatascience.com/time-llm-reprogram-an-llm-for-time-series-forecasting-e2558087b8ac?source=collection_archive---------0-----------------------#2024-03-05](https://towardsdatascience.com/time-llm-reprogram-an-llm-for-time-series-forecasting-e2558087b8ac?source=collection_archive---------0-----------------------#2024-03-05)

## 了解Time-LLM的架构并在Python中应用于预测项目

[](https://medium.com/@marcopeixeiro?source=post_page---byline--e2558087b8ac--------------------------------)[![Marco Peixeiro](../Images/7cf0a81d87281d35ff47f51e3026a3e9.png)](https://medium.com/@marcopeixeiro?source=post_page---byline--e2558087b8ac--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e2558087b8ac--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e2558087b8ac--------------------------------) [Marco Peixeiro](https://medium.com/@marcopeixeiro?source=post_page---byline--e2558087b8ac--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e2558087b8ac--------------------------------) ·阅读时间12分钟·2024年3月5日

--

![](../Images/0aa80a24f282c84669c39c8c0bb099a1.png)

图片由[Zdeněk Macháček](https://unsplash.com/@zmachacek?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

这并不是研究人员第一次尝试将自然语言处理（NLP）技术应用于时间序列领域。

例如，Transformer架构是NLP中的一个重要里程碑，但其在时间序列预测中的表现一直平平，直到[PatchTST](https://medium.com/towards-data-science/patchtst-a-breakthrough-in-time-series-forecasting-e02d48869ccc)的提出。

如你所知，大型语言模型（LLM）正在积极开发，并已展示出在自然语言处理（NLP）中的令人印象深刻的泛化和推理能力。

因此，值得探讨将大型语言模型（LLM）用于时间序列预测的思路，以便我们能够充分利用这些大型预训练模型的能力。

为此，[Time-LLM](https://arxiv.org/pdf/2310.01728.pdf)应运而生。在原始论文中，研究人员提出了一个框架，将现有的LLM重新编程以执行时间序列预测。

在本文中，我们探讨了Time-LLM的架构及其如何有效地让LLM进行时间序列数据预测。接着，我们实现了该模型并将其应用于一个小型预测项目。

如需更多详情，请务必阅读[原始论文](https://arxiv.org/pdf/2310.01728.pdf)。
