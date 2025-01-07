# 多臂赌博机问题——面向初学者的指南

> 原文：[https://towardsdatascience.com/the-multi-armed-bandit-problem-a-beginner-friendly-guide-2293ce7d8da8?source=collection_archive---------5-----------------------#2024-12-23](https://towardsdatascience.com/the-multi-armed-bandit-problem-a-beginner-friendly-guide-2293ce7d8da8?source=collection_archive---------5-----------------------#2024-12-23)

## 通过一个例子来理解**利用-探索**权衡

[](https://saankhya.medium.com/?source=post_page---byline--2293ce7d8da8--------------------------------)[![Saankhya Mondal](../Images/b22ffe3b52c6c3bcfafaeed3812811d8.png)](https://saankhya.medium.com/?source=post_page---byline--2293ce7d8da8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2293ce7d8da8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2293ce7d8da8--------------------------------) [Saankhya Mondal](https://saankhya.medium.com/?source=post_page---byline--2293ce7d8da8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2293ce7d8da8--------------------------------) ·阅读时间6分钟·2024年12月23日

--

**多臂赌博机（MAB）**是决策中的经典问题，其中一个代理人必须在多个选择（称为“臂”）之间做出决策，并通过一系列试验最大化总奖励。这个问题得名于一个比喻，描述了一个赌徒面对一排老虎机（单臂老虎机），每个老虎机的支付概率不同且未知。目标是找到最佳策略来拉动老虎机（选择行动），并随着时间的推移最大化赌徒的总体奖励。MAB问题是**利用-探索**权衡的一个华丽称谓。

多臂赌博机问题是一个基础性问题，广泛应用于多个工业领域。让我们来探讨它，并审视一些有趣的解决策略。

![](../Images/9eb718febf14f8afb356b9c3de3205b8.png)

图片由Grok生成

# 示例

假设你刚到一个新城市。你是一个间谍，计划待120天以完成下一个任务。镇上有三家餐馆：意大利餐厅、中华餐厅和墨西哥餐厅。你希望在逗留期间最大化自己的用餐满意度。然而，你不知道哪家餐厅最适合你。以下是这三家餐厅的情况：

+   **意大利餐厅**：平均满意度得分为……
