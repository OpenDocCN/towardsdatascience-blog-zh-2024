# 使用 MLflow 追踪计算机视觉实验

> 原文：[`towardsdatascience.com/track-computer-vision-experiments-with-mlflow-3852f557b27a?source=collection_archive---------4-----------------------#2024-12-26`](https://towardsdatascience.com/track-computer-vision-experiments-with-mlflow-3852f557b27a?source=collection_archive---------4-----------------------#2024-12-26)

## 发现如何设置高效的 MLflow 环境，以跟踪你的实验，比较并选择最适合部署的模型

[](https://yagmurcigdemaktas.medium.com/?source=post_page---byline--3852f557b27a--------------------------------)![Yağmur Çiğdem Aktaş](https://yagmurcigdemaktas.medium.com/?source=post_page---byline--3852f557b27a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3852f557b27a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3852f557b27a--------------------------------) [Yağmur Çiğdem Aktaş](https://yagmurcigdemaktas.medium.com/?source=post_page---byline--3852f557b27a--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3852f557b27a--------------------------------) ·9 分钟阅读·2024 年 12 月 26 日

--

训练和微调各种模型是每个计算机视觉研究人员的基础任务。即使是简单的模型，我们也会进行超参数搜索，以找到在我们自定义数据集上训练模型的最佳方式。数据增强技术（已包括许多不同的选项）、优化器的选择、学习率以及模型本身。这个架构对我来说是最合适的吗？我是否应该添加更多的层，改变架构，很多问题都等待着被提问和探索？

在寻找所有这些问题的答案时，我曾将模型训练过程的日志文件和输出检查点保存在本地的不同文件夹中，每次运行训练时都更改输出目录名称，并手动一项一项比较最终的指标。以如此手动的方式处理实验跟踪过程有许多缺点：它是老派的，耗时耗力，且容易出错。

在这篇博客中，我将向你展示如何使用 MLflow，这是一个追踪实验的最佳工具之一，能够记录你需要的任何信息，直观地比较你完成的不同训练实验，并在一个用户友好（也符合眼睛友好）环境中，帮助你决定哪个训练是最佳选择！
