# AI天气模型现在超越传统预报

> 原文：[https://towardsdatascience.com/ai-weather-models-now-outperform-traditional-forecasts-2ad6e33d4346?source=collection_archive---------4-----------------------#2024-03-11](https://towardsdatascience.com/ai-weather-models-now-outperform-traditional-forecasts-2ad6e33d4346?source=collection_archive---------4-----------------------#2024-03-11)

## 它们是如何被评估的，这对未来的天气预报意味着什么？

[](https://medium.com/@caroline.arnold_63207?source=post_page---byline--2ad6e33d4346--------------------------------)[![Caroline Arnold](../Images/fb13ba36e302d8161b67c4888d0601e4.png)](https://medium.com/@caroline.arnold_63207?source=post_page---byline--2ad6e33d4346--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2ad6e33d4346--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2ad6e33d4346--------------------------------) [Caroline Arnold](https://medium.com/@caroline.arnold_63207?source=post_page---byline--2ad6e33d4346--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2ad6e33d4346--------------------------------) ·阅读时间7分钟·2024年3月11日

--

![](../Images/9edb004aad336d55303d7823f749818d.png)

图片由作者使用[Midjourney](https://www.midjourney.com/jobs/e2c08feb-e55b-4151-b007-e427b651ffa1?index=0)创作。

你是否曾查看过周末的天气预报并做了计划，却被雨水打乱了安排？那么你可能会对天气预报的可靠性产生疑问。

预测天气是困难的。大气是一个充满不确定性的混沌系统。传统的天气模型需要复杂的数值方法来预测大气的未来状态。

在过去五十年中，气象服务已经建立了一个包含[*重分析数据*](https://climate.copernicus.eu/climate-reanalysis)的天气档案。这个档案在卫星观测、地面气象站和数值天气预报的推动下，尽可能地代表了我们对过去天气的了解。

## 天气预报中的AI革命

五年前，天气预报中的AI革命开始了。研究人员使用过去50年的重分析天气档案，开发了[AI模型](https://github.com/pangeo-data/WeatherBench)来预测天气。

这些模型通过训练数据学习天气模式。在推断时，它们获取当前大气状态并输出未来的状态。这可以反复应用，允许模型进行…
