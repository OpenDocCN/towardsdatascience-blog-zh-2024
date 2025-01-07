# AutoGluon-TimeSeries：一个库包含所有时间序列预测模型

> 原文：[https://towardsdatascience.com/autogluon-timeseries-every-time-series-forecasting-model-in-one-library-29a3bf6879db?source=collection_archive---------3-----------------------#2024-01-05](https://towardsdatascience.com/autogluon-timeseries-every-time-series-forecasting-model-in-one-library-29a3bf6879db?source=collection_archive---------3-----------------------#2024-01-05)

## 由亚马逊推出的强大库——包括编码示例

[](https://medium.com/@nikoskafritsas?source=post_page---byline--29a3bf6879db--------------------------------)[![Nikos Kafritsas](../Images/de965cfcd8fbd8e1baf849017d365cbb.png)](https://medium.com/@nikoskafritsas?source=post_page---byline--29a3bf6879db--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--29a3bf6879db--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--29a3bf6879db--------------------------------) [Nikos Kafritsas](https://medium.com/@nikoskafritsas?source=post_page---byline--29a3bf6879db--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--29a3bf6879db--------------------------------) ·阅读时间 8 分钟·2024年1月5日

--

![](../Images/9f3b2178013b967460ce499a11e7fd86.png)

图像由作者使用 Stable Diffusion 创建

**开源时间序列领域正在加速发展。**

其中包括成功的库，如 Darts、GluonTS 和 Nixtla。

去年，亚马逊扩展了其 AutoGluon 库，专注于时间序列——名为**AutoGluon-Timeseries(AG-TS)[1]**。

AG-TS 利用其他库的专业知识：

+   来自亚马逊自身（AutoGluon 和 GluonTS）。

+   来自 Nixtla（StatsForecast 和 MLForecast）。

最棒的部分是：AG-TS 拥有用户友好的 API——**我们只需几行代码就能获取预测！**

本文探讨了 AG-TS，并概述了它的功能。我们还将构建一个简单的项目，使用广为人知的**旅游数据集[2]**。

让我们深入了解

> 我推出了[**AI Horizon Forecast**](https://aihorizonforecast.substack.com/)**，**这是一个专注于时间序列和创新 AI 研究的新闻通讯。点击[这里](https://aihorizonforecast.substack.com/)订阅，拓宽你的视野！

# 什么是 AutoGluon-Timeseries

> AutoGluon–TimeSeries 是一个 AutoML 时间序列框架，专注于……
