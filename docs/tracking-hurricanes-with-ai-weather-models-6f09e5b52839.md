# 使用AI气象模型追踪飓风

> 原文：[https://towardsdatascience.com/tracking-hurricanes-with-ai-weather-models-6f09e5b52839?source=collection_archive---------6-----------------------#2024-10-11](https://towardsdatascience.com/tracking-hurricanes-with-ai-weather-models-6f09e5b52839?source=collection_archive---------6-----------------------#2024-10-11)

## 数据科学家的风暴追踪：飓风米尔顿案例研究

[](https://medium.com/@caroline.arnold_63207?source=post_page---byline--6f09e5b52839--------------------------------)[![Caroline Arnold](../Images/fb13ba36e302d8161b67c4888d0601e4.png)](https://medium.com/@caroline.arnold_63207?source=post_page---byline--6f09e5b52839--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6f09e5b52839--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6f09e5b52839--------------------------------) [Caroline Arnold](https://medium.com/@caroline.arnold_63207?source=post_page---byline--6f09e5b52839--------------------------------)

·发布于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--6f09e5b52839--------------------------------) ·6分钟阅读·2024年10月11日

--

![](../Images/76d32d1c1abcab4eb6d44c2b930890a7.png)

图片由[Zoshua Colah](https://unsplash.com/@zoshuacolah?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

2024年10月9日星期三，[飓风米尔顿](https://en.wikipedia.org/wiki/Hurricane_Milton#Meteorological_history)登陆。风暴在傍晚时分到达佛罗里达西海岸。这是2024年迄今为止最强的热带气旋。

在这里，我探索了AI气象模型[PanguWeather](https://www.google.com/url?sa=t&rct=j&opi=89978449&url=https%3A%2F%2Fwww.nature.com%2Farticles%2Fs41586-023-06185-3&ved=2ahUKEwiNure1-4GJAxUGB9sEHZL3JOoQFnoECBQQAQ&usg=AOvVaw225Adu3_z60DZm6X0Wznaw)是如何预测飓风米尔顿的。本文包括了代码片段，帮助你复制并扩展我的分析。

AI气象模型使用历史天气数据来预测今天的天气。

要生成你自己的AI天气预报，你需要一个预训练的天气模型和初始化数据。这些都可以通过[Github](https://github.com/ecmwf-lab/ai-models)从欧洲中期天气预报中心（ECMWF）获得。

我使用了[欧洲中期天气预报中心](https://github.com/ecmwf-lab/ai-models)提供的[预训练模型和初始化数据](https://github.com/ecmwf-lab/ai-models)来创建全球天气预报。

按照[安装说明](https://github.com/ecmwf-lab/ai-models?tab=readme-ov-file#ai-models)进行操作，开始进行预报：

```py
ai-models --assets panguweather --input ecmwf-open-data panguweather
```

我的预测的开始日期是当地时间（EDT）10月8日，星期二，上午08:00。 在一台笔记本电脑的CPU上完成此任务花费了1.4小时。 使用GPU的话，PanguWeather可以在几分钟内提供全球预报。
