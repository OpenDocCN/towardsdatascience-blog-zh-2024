# 如何使用生成式 AI 和 Python 创建设计师虚拟数据集

> 原文：[`towardsdatascience.com/how-to-use-generative-ai-and-python-to-create-designer-dummy-datasets-d3cd9755c091?source=collection_archive---------6-----------------------#2024-04-08`](https://towardsdatascience.com/how-to-use-generative-ai-and-python-to-create-designer-dummy-datasets-d3cd9755c091?source=collection_archive---------6-----------------------#2024-04-08)

## 一个简单的实际应用指南

[](https://medium.com/@mia_dwyer?source=post_page---byline--d3cd9755c091--------------------------------)![Mia Dwyer](https://medium.com/@mia_dwyer?source=post_page---byline--d3cd9755c091--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d3cd9755c091--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d3cd9755c091--------------------------------) [Mia Dwyer](https://medium.com/@mia_dwyer?source=post_page---byline--d3cd9755c091--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d3cd9755c091--------------------------------) ·6 分钟阅读·2024 年 4 月 8 日

--

你是否曾经需要一个不存在的、难以找到的数据集？是否曾想过轻松生成符合你精确需求的数据，用于面试潜在的数据科学候选人、软件测试与开发，或者训练模型？或者，单纯只是想获得合适的数据，来展示技能和技术用于撰写 Medium 文章（且不违反版权法）？

进入虚拟数据！📊✨

![](img/1dd939643ef8e6d79db67b64dedd4566.png)

图片由我创建，使用 DALL-E

直到最近，创建虚拟数据集还是一项相对繁琐且费力的工作，技术人员可以通过精心编写的 Python 代码来生成这些数据集，但手动编写所有的需求代码既耗时又具有较高的技术门槛。

假设我们有一个用例，想要测试一名申请金融科技数据科学岗位的候选人，并且我们希望他们能够识别并讨论一些真实世界中的模式，但出于隐私原因，我们无法共享实际的客户交易数据。

> 解决方案？利用生成式 AI 的强大功能，巧妙地编写复杂的 Python 代码来输出我们的✨设计师虚拟数据集✨
