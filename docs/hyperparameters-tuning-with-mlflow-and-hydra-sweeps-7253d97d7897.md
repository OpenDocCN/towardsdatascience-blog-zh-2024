# MLOps — 使用 MLflow 和 Hydra 进行超参数调优

> 原文：[`towardsdatascience.com/hyperparameters-tuning-with-mlflow-and-hydra-sweeps-7253d97d7897?source=collection_archive---------6-----------------------#2024-04-25`](https://towardsdatascience.com/hyperparameters-tuning-with-mlflow-and-hydra-sweeps-7253d97d7897?source=collection_archive---------6-----------------------#2024-04-25)

![](img/b1361f4c04ef4d3828f567feb107e982.png)

照片来自[Leo_Visions](https://unsplash.com/@leo_visions_?utm_source=medium&utm_medium=referral)，发布于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 学习如何使用 Hydra 和 MLflow 构建高效的管道

[](https://medium.com/@marcellopoliti?source=post_page---byline--7253d97d7897--------------------------------)![Marcello Politi](https://medium.com/@marcellopoliti?source=post_page---byline--7253d97d7897--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7253d97d7897--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7253d97d7897--------------------------------) [Marcello Politi](https://medium.com/@marcellopoliti?source=post_page---byline--7253d97d7897--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7253d97d7897--------------------------------) ·阅读时间：7 分钟·2024 年 4 月 25 日

--

## 介绍

在我们开发机器学习模型时，通常需要进行大量实验，以找出哪个超参数设置最适合给定的算法。这常常会导致代码混乱，并且难以追踪哪个结果对应哪个设置。我经常看到人们硬编码超参数，启动实验，然后把结果记录在 Excel 文件中。我相信我们可以改进这个工作流。

[在我上一篇文章中，我讨论了如何使用 MLflow 进行管道化](https://medium.com/towards-data-science/mlops-a-gentle-introduction-to-mlflow-pipelines-c7bcec88a6ec)。

[](/mlops-a-gentle-introduction-to-mlflow-pipelines-c7bcec88a6ec?source=post_page-----7253d97d7897--------------------------------) ## MlOps — 轻松入门 Mlflow Pipelines

### 使用 MLflow 编排端到端的机器学习生命周期

towardsdatascience.com

今天我想增加一些复杂性，并解释如何将 Hydra 集成进来。Hydra 是一个出色的开源工具，除了其他功能外，它还允许你使用不同的模型设置来运行测试。

我使用[Deepnote](https://deepnote.com/)运行本篇文章中的脚本：这是一个基于云的笔记本，适合协作数据科学项目和原型开发。

## 让我们开始编码吧！
