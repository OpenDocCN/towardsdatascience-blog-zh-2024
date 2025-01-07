# MLOps — MLflow Pipelines 的温和入门

> 原文：[`towardsdatascience.com/mlops-a-gentle-introduction-to-mlflow-pipelines-c7bcec88a6ec?source=collection_archive---------0-----------------------#2024-03-13`](https://towardsdatascience.com/mlops-a-gentle-introduction-to-mlflow-pipelines-c7bcec88a6ec?source=collection_archive---------0-----------------------#2024-03-13)

![](img/2a1eb375a43d2db38d0e8e49dd56848a.png)

图片由[Sean Robertson](https://unsplash.com/@knuknuk?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 使用 MLflow 管理您的端到端机器学习生命周期

[](https://medium.com/@marcellopoliti?source=post_page---byline--c7bcec88a6ec--------------------------------)![Marcello Politi](https://medium.com/@marcellopoliti?source=post_page---byline--c7bcec88a6ec--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c7bcec88a6ec--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c7bcec88a6ec--------------------------------) [Marcello Politi](https://medium.com/@marcellopoliti?source=post_page---byline--c7bcec88a6ec--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c7bcec88a6ec--------------------------------) ·8 分钟阅读·2024 年 3 月 13 日

--

# 介绍

各种统计数据显示，[**50% 到 90% 的模型未能成功投入生产**](https://venturebeat.com/ai/why-do-87-of-data-science-projects-never-make-it-into-production/)。这通常是由于未能有效地组织工作流程。学术界（或 Kaggle 上）获得的技能，往往不足以支撑一个可以被成千上万用户使用的机器学习系统。

在寻找机器学习行业工作时，最为抢手的技能之一就是能够使用能协调复杂流程的工具，如 MLflow。

在本文中，我们将了解如何将一个项目结构化为多个步骤，并以有序的方式管理所有步骤。

我在[Deepnote](https://deepnote.com/)上运行本文的脚本：一个基于云的笔记本，非常适合协作数据科学项目和原型设计。

## 什么是 MLflow？

[MLflow](https://mlflow.org/) 是一个由[Databricks](https://www.databricks.com/)开发的开源平台，用于机器学习的端到端生命周期管理。

MLflow 提供多种功能，如监控训练中的模型，使用工件存储等……
