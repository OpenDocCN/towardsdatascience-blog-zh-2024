# MOIRAI：Salesforce的时间序列预测基础模型

> 原文：[https://towardsdatascience.com/moirai-salesforces-foundation-model-for-time-series-forecasting-4eff6c34093d?source=collection_archive---------2-----------------------#2024-03-15](https://towardsdatascience.com/moirai-salesforces-foundation-model-for-time-series-forecasting-4eff6c34093d?source=collection_archive---------2-----------------------#2024-03-15)

## 代码、模型权重和数据将在不久后发布

[](https://medium.com/@nikoskafritsas?source=post_page---byline--4eff6c34093d--------------------------------)[![Nikos Kafritsas](../Images/de965cfcd8fbd8e1baf849017d365cbb.png)](https://medium.com/@nikoskafritsas?source=post_page---byline--4eff6c34093d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4eff6c34093d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--4eff6c34093d--------------------------------) [Nikos Kafritsas](https://medium.com/@nikoskafritsas?source=post_page---byline--4eff6c34093d--------------------------------)

·发表于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--4eff6c34093d--------------------------------) ·10分钟阅读·2024年3月15日

--

![](../Images/f65123f28c59c301de258480d8133f51.png)

图片来源：[1]

**时间序列基础模型终于迎来了突破！**

前面的文章探讨了2个有前景的基础预测模型，TimeGPT和[TimesFM](https://medium.com/towards-data-science/timesfm-googles-foundation-model-for-time-series-forecasting-593a332dd08d)。

本文将探讨***MOIRAI* [1]**，这是Salesforce推出的开创性时间序列基础模型。*MOIRAI*在性能上表现出色——但更重要的是，作者们承诺将开源该模型及其训练数据集！

这点在Caiming Xiong（Salesforce AI副总裁，论文作者之一）的推文中有提到[这里](https://twitter.com/CaimingXiong)

本文的主要贡献如下：

+   **MOIRAI**：一种新型的变压器编码器架构，作为一个通用的时间序列预测模型。

+   **LOTSA**（*大型开放时间序列档案*）：全球最大的开放时间序列数据集，涵盖9个领域，共计270亿条观测数据。

+   **UNITS**：一个用于训练通用时间序列模型的开源库。

此外，本文还讨论了：

1.  *MOIRAI*的工作原理及其强大之处。

1.  *MOIRAI*与谷歌的TimesFM相比的表现
