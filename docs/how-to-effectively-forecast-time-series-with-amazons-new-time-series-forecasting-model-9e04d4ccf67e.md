# 如何有效地使用亚马逊的新时间序列预测模型进行时间序列预测

> 原文：[https://towardsdatascience.com/how-to-effectively-forecast-time-series-with-amazons-new-time-series-forecasting-model-9e04d4ccf67e?source=collection_archive---------6-----------------------#2024-04-09](https://towardsdatascience.com/how-to-effectively-forecast-time-series-with-amazons-new-time-series-forecasting-model-9e04d4ccf67e?source=collection_archive---------6-----------------------#2024-04-09)

## 了解亚马逊新的时间序列模型，您可以使用它来预测能源使用、交通拥堵和天气。

[](https://oieivind.medium.com/?source=post_page---byline--9e04d4ccf67e--------------------------------)[![Eivind Kjosbakken](../Images/5f91b74428e1202fc4a176a3dd1cb1c7.png)](https://oieivind.medium.com/?source=post_page---byline--9e04d4ccf67e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9e04d4ccf67e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--9e04d4ccf67e--------------------------------) [Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--9e04d4ccf67e--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9e04d4ccf67e--------------------------------) ·阅读时间12分钟·2024年4月9日

--

我将讨论亚马逊新的Chronos时间序列预测模型[1]。该模型可用于多种时间序列预测任务，例如预测能源使用、交通/拥堵预测或天气预测。这使得它既灵活又强大。我将讨论该模型的性能、优缺点，以及如何在本地实现和运行该模型。

![](../Images/f1fe1e1e46debbdc4694528d15ce2dd7.png)

图像来自ChatGPT，关于可视化时间序列预测。图像由ChatGPT提供。OpenAI。（2024年）。*ChatGPT*（4）[大型语言模型]。 [https://chat.openai.com](https://chat.openai.com)

# 动机

本文的动机是跟进机器学习领域的最新模型。我通过查看PapersWithCode，了解到了这个模型。PapersWithCode是我定期查看的一个网站，帮助我跟上机器学习领域的最新趋势。每当我发现有趣的内容时，我喜欢将其实现并感受该模型及其性能。本文将讨论如何使用该模型、该模型可以应用于哪些任务，以及我对该模型性能的看法。

在运行该模型后，您将能够像下图所示那样进行模型预测：
