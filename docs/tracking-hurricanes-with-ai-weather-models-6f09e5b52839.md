# 使用 AI 气象模型追踪飓风

> 原文：[`towardsdatascience.com/tracking-hurricanes-with-ai-weather-models-6f09e5b52839?source=collection_archive---------6-----------------------#2024-10-11`](https://towardsdatascience.com/tracking-hurricanes-with-ai-weather-models-6f09e5b52839?source=collection_archive---------6-----------------------#2024-10-11)

## 数据科学家的风暴追踪：飓风米尔顿案例研究

[](https://medium.com/@caroline.arnold_63207?source=post_page---byline--6f09e5b52839--------------------------------)![Caroline Arnold](https://medium.com/@caroline.arnold_63207?source=post_page---byline--6f09e5b52839--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6f09e5b52839--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6f09e5b52839--------------------------------) [Caroline Arnold](https://medium.com/@caroline.arnold_63207?source=post_page---byline--6f09e5b52839--------------------------------)

·发布于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--6f09e5b52839--------------------------------) ·6 分钟阅读·2024 年 10 月 11 日

--

![](img/76d32d1c1abcab4eb6d44c2b930890a7.png)

图片由[Zoshua Colah](https://unsplash.com/@zoshuacolah?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

2024 年 10 月 9 日星期三，[飓风米尔顿](https://en.wikipedia.org/wiki/Hurricane_Milton#Meteorological_history)登陆。风暴在傍晚时分到达佛罗里达西海岸。这是 2024 年迄今为止最强的热带气旋。

在这里，我探索了 AI 气象模型[PanguWeather](https://www.google.com/url?sa=t&rct=j&opi=89978449&url=https%3A%2F%2Fwww.nature.com%2Farticles%2Fs41586-023-06185-3&ved=2ahUKEwiNure1-4GJAxUGB9sEHZL3JOoQFnoECBQQAQ&usg=AOvVaw225Adu3_z60DZm6X0Wznaw)是如何预测飓风米尔顿的。本文包括了代码片段，帮助你复制并扩展我的分析。

AI 气象模型使用历史天气数据来预测今天的天气。

要生成你自己的 AI 天气预报，你需要一个预训练的天气模型和初始化数据。这些都可以通过[Github](https://github.com/ecmwf-lab/ai-models)从欧洲中期天气预报中心（ECMWF）获得。

我使用了[欧洲中期天气预报中心](https://github.com/ecmwf-lab/ai-models)提供的[预训练模型和初始化数据](https://github.com/ecmwf-lab/ai-models)来创建全球天气预报。

按照[安装说明](https://github.com/ecmwf-lab/ai-models?tab=readme-ov-file#ai-models)进行操作，开始进行预报：

```py
ai-models --assets panguweather --input ecmwf-open-data panguweather
```

我的预测的开始日期是当地时间（EDT）10 月 8 日，星期二，上午 08:00。 在一台笔记本电脑的 CPU 上完成此任务花费了 1.4 小时。 使用 GPU 的话，PanguWeather 可以在几分钟内提供全球预报。
