# 使用 MLflow、Azure 和 Docker 进行模型管理

> 原文：[`towardsdatascience.com/model-management-with-mlflow-azure-and-docker-2920b51a5bdd?source=collection_archive---------2-----------------------#2024-09-17`](https://towardsdatascience.com/model-management-with-mlflow-azure-and-docker-2920b51a5bdd?source=collection_archive---------2-----------------------#2024-09-17)

## 跟踪实验和管理模型的指南

[](https://medium.com/@sabrine.bendimerad1?source=post_page---byline--2920b51a5bdd--------------------------------)![Sabrine Bendimerad](https://medium.com/@sabrine.bendimerad1?source=post_page---byline--2920b51a5bdd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2920b51a5bdd--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2920b51a5bdd--------------------------------) [Sabrine Bendimerad](https://medium.com/@sabrine.bendimerad1?source=post_page---byline--2920b51a5bdd--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2920b51a5bdd--------------------------------) ·阅读时长 10 分钟·2024 年 9 月 17 日

--

![](img/cdb0e3cb96859fbe8dc739602ec2e023.png)

[pixabay.com](https://pixabay.com/)

在第一篇文章中，我们探索了 Docker 将应用程序及其依赖项打包成可移植容器的强大能力，确保在各种环境中保持一致性。

在此基础上，本文介绍了**MLflow**，这是一个在机器学习工作流中用于实验追踪和模型管理的重要工具。我们将演示如何在 Docker 容器内部署和使用 MLflow，以确保可移植性并避免与依赖项相关的问题。容器化的 MLflow 服务器将部署在**Azure**上，以提供更好的可扩展性、远程访问，且更重要的是团队协作。

# 什么是 MLflow

MLflow 是一个开源平台，简化了机器学习生命周期的管理，从实验追踪到模型部署。它提供了一个稳定的框架，用于记录实验、管理代码和追踪模型版本，确保你的工作流程在团队间是可复现的且井然有序。

MLflow 可以集成到你机器学习管道的各个阶段。它提供了四个主要组件：

+   **MLflow Tracking**：这是最广泛使用的功能，允许你记录和查询实验。它追踪有用的细节……
