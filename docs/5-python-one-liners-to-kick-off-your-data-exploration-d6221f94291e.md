# 启动数据探索的 5 个 Python 一行代码

> 原文：[https://towardsdatascience.com/5-python-one-liners-to-kick-off-your-data-exploration-d6221f94291e?source=collection_archive---------0-----------------------#2024-12-06](https://towardsdatascience.com/5-python-one-liners-to-kick-off-your-data-exploration-d6221f94291e?source=collection_archive---------0-----------------------#2024-12-06)

## 如何使用一行代码启动你的 EDA

[](https://medium.com/@pelletierhaden?source=post_page---byline--d6221f94291e--------------------------------)[![Haden Pelletier](../Images/8f73fc8222e783883c4ebcaee14513e0.png)](https://medium.com/@pelletierhaden?source=post_page---byline--d6221f94291e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d6221f94291e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d6221f94291e--------------------------------) [Haden Pelletier](https://medium.com/@pelletierhaden?source=post_page---byline--d6221f94291e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d6221f94291e--------------------------------) ·6 分钟阅读 ·2024年12月6日

--

![](../Images/11a2589005ed21547bde9f021ab72f6a.png)

图片来自 [Lukas Blazek](https://www.paypal.me/goumbik) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 上

# 探索性数据分析

当谈到机器学习时，探索性数据分析（EDA）是你在将数据收集并加载到 Python 中后需要做的第一件事之一。

EDA 涉及：

+   通过描述性统计总结数据

+   可视化数据

+   识别模式、检测异常和生成假设

通过 EDA，数据科学家能够深入了解数据，从而评估数据质量，并为更复杂的机器学习任务做好准备。

但有时在刚开始时，可能会遇到挑战，不知道从哪里入手。

这里有 5 个简单的 Python 一行代码，能帮助你快速启动 EDA 过程。

## 1\. df.info()

这是每个 EDA 过程中的必备步骤。事实上，这是我加载数据框（df）后运行的第一行代码。

它告诉你：

+   列的名称

+   每一列中有多少个非空值

+   列的数据类型
