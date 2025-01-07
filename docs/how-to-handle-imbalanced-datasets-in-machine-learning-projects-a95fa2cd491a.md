# 如何处理机器学习项目中的不平衡数据集

> 原文：[https://towardsdatascience.com/how-to-handle-imbalanced-datasets-in-machine-learning-projects-a95fa2cd491a?source=collection_archive---------8-----------------------#2024-10-03](https://towardsdatascience.com/how-to-handle-imbalanced-datasets-in-machine-learning-projects-a95fa2cd491a?source=collection_archive---------8-----------------------#2024-10-03)

## 处理不平衡数据集的技术、示例及Python代码片段

[](https://medium.com/@jiayanyin.simba?source=post_page---byline--a95fa2cd491a--------------------------------)[![Jiayan Yin](../Images/1a67e16a388877478366a8c6b2736dda.png)](https://medium.com/@jiayanyin.simba?source=post_page---byline--a95fa2cd491a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a95fa2cd491a--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a95fa2cd491a--------------------------------) [Jiayan Yin](https://medium.com/@jiayanyin.simba?source=post_page---byline--a95fa2cd491a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a95fa2cd491a--------------------------------) ·阅读时长9分钟·2024年10月3日

--

![](../Images/343cab07909551acab9da44713a73bdd.png)

图片来源：[Nick Fewings](https://unsplash.com/@jannerboy62?utm_source=medium&utm_medium=referral) 来自 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

假设你训练了一个预测模型，准确率高达0.9。像精确度、召回率和F1分数等评估指标看起来也很有前景。但你的经验和直觉告诉你事情并不对劲，于是你进行了进一步调查，发现了以下情况：

![](../Images/86c5299ee5a6d712c32563bc2eb0c8c4.png)

Image_1 — 作者截图

该模型看似强大的表现是由其目标变量中占多数的类别`0`驱动的。由于多数类与少数类之间明显的***不平衡***，模型在预测其多数类`0`时表现优秀，而少数类`1`的表现则远远不尽人意。然而，由于类别`1`仅占目标变量的一小部分，其表现对这些评估指标的整体得分影响不大，这给人一种模型很强的错觉。

这并非罕见的情况。相反，数据科学家在实际项目中经常会遇到不平衡的数据集。*不平衡数据集*指的是类别或种类分布不均的数据集……
