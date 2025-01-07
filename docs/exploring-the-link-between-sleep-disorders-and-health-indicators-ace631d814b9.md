# 探索睡眠障碍与健康指标之间的关系

> 原文：[`towardsdatascience.com/exploring-the-link-between-sleep-disorders-and-health-indicators-ace631d814b9?source=collection_archive---------3-----------------------#2024-09-28`](https://towardsdatascience.com/exploring-the-link-between-sleep-disorders-and-health-indicators-ace631d814b9?source=collection_archive---------3-----------------------#2024-09-28)

## 使用 Python 对 MIMIC-IV 健康数据（DREAMT）进行分析，探索影响睡眠障碍的因素。

[](https://medium.com/@aramary?source=post_page---byline--ace631d814b9--------------------------------)![Mary Ara](https://medium.com/@aramary?source=post_page---byline--ace631d814b9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ace631d814b9--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ace631d814b9--------------------------------) [Mary Ara](https://medium.com/@aramary?source=post_page---byline--ace631d814b9--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ace631d814b9--------------------------------) ·阅读时长 14 分钟·2024 年 9 月 28 日

--

![](img/a98e9ed9aae1754d9dbcf590560eb4eb.png)

图片由[Benjamin Voros](https://unsplash.com/@vorosbenisop?utm_source=medium&utm_medium=referral)提供，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在本文中，我将分析 DREAMT 数据集中的参与者信息，以揭示睡眠障碍（如睡眠呼吸暂停、打鼾、呼吸困难、头痛、静息腿综合症（RLS）、打呼噜等）与参与者特征（如年龄、性别、体质指数（BMI）、觉醒指数、平均氧饱和度（Mean_SaO2）、病史、阻塞性呼吸暂停-低通气指数（OAHI）和呼吸暂停-低通气指数（AHI））之间的关系。

这里的参与者是那些参加了 DREAMT 研究的人员。

最终结果将是一个综合的数据分析报告，包含可视化内容、洞察分析和结论。

# 工具

我将使用一个 Jupyter 笔记本，并结合 Pandas、Numpy、Matplotlib 和 Seaborn 等 Python 库进行分析。

# 数据

用于本次分析的数据来自 DREAMT：使用多传感器可穿戴技术进行实时睡眠阶段估计的数据集 1.0.1。DREAMT 是 PhysioNet 托管的 MIMIC-IV 数据集的一部分。
