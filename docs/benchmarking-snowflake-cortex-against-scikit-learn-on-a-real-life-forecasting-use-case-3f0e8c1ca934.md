# 将 Snowflake Cortex 与 Scikit-Learn 在实际预测用例中的表现进行基准测试。

> 原文：[https://towardsdatascience.com/benchmarking-snowflake-cortex-against-scikit-learn-on-a-real-life-forecasting-use-case-3f0e8c1ca934?source=collection_archive---------2-----------------------#2024-02-25](https://towardsdatascience.com/benchmarking-snowflake-cortex-against-scikit-learn-on-a-real-life-forecasting-use-case-3f0e8c1ca934?source=collection_archive---------2-----------------------#2024-02-25)

## 作为目前最流行的基于云的数据平台之一，Snowflake 现在嵌入了高级建模功能，我也尝试了其中的预测功能。

[](https://pl-bescond.medium.com/?source=post_page---byline--3f0e8c1ca934--------------------------------)[![Pierre-Louis Bescond](../Images/bb236055962b420fb3ab22088ab28f11.png)](https://pl-bescond.medium.com/?source=post_page---byline--3f0e8c1ca934--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3f0e8c1ca934--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3f0e8c1ca934--------------------------------) [Pierre-Louis Bescond](https://pl-bescond.medium.com/?source=post_page---byline--3f0e8c1ca934--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3f0e8c1ca934--------------------------------) ·9分钟阅读·2024年2月25日

--

![](../Images/c936b129ca874c9fceb08c6e6ccc29a4.png)

一个戏剧性的雪旋风 — 由作者通过 [Leornardo.ai](https://app.leonardo.ai/) 生成

几个月前（2023年11月23日），Snowflake 宣布发布了多项新的建模/大语言模型（LLM）功能，这些功能属于一个名为 “[Cortex](https://www.snowflake.com/blog/use-ai-snowflake-cortex/)” 的框架。

自12月中旬以来，前两项功能（[预测](https://docs.snowflake.com/en/user-guide/ml-powered-forecasting) 和 [异常检测](https://docs.snowflake.com/en/user-guide/ml-powered-anomaly-detection)）已经全面开放使用（见 [Snowflake 7.44 版本说明](https://docs.snowflake.com/en/release-notes/2023/7_44#sql-updates)）。

因此，Snowflake 继续其使命，提供一个完全托管的“一站式”分析平台，帮助数据使用者从其数据资产中释放价值，除了面向数据工程团队的常规数据仓库功能之外。

这些功能让一些人联想到了 “[Google BigQuery ML](https://cloud.google.com/bigquery/docs/bqml-introduction)” 的功能，后者最初在 [2020年8月](https://cloud.google.com/bigquery/docs/release-notes#August_27_2020) 发布（是的，四年前！）；让我们深入了解一下吧！

## 预测本地城市游泳池的访问量

除了 Snowday ❄️ 上令人兴奋的演讲和量身定制的展示外，我迫不及待地想将一个真实的数据集加载到 Snowflake 中，看看 Cortex 相比于传统方法的表现如何。
