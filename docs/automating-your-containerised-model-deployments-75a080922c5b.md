# 自动化您的容器化模型部署

> 原文：[`towardsdatascience.com/automating-your-containerised-model-deployments-75a080922c5b?source=collection_archive---------6-----------------------#2024-04-22`](https://towardsdatascience.com/automating-your-containerised-model-deployments-75a080922c5b?source=collection_archive---------6-----------------------#2024-04-22)

## 使用 AWS、Terraform、Ansible 和 GitLab-CI

[](https://medium.com/@teosiyang?source=post_page---byline--75a080922c5b--------------------------------)![Jake Teo](https://medium.com/@teosiyang?source=post_page---byline--75a080922c5b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--75a080922c5b--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--75a080922c5b--------------------------------) [Jake Teo](https://medium.com/@teosiyang?source=post_page---byline--75a080922c5b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--75a080922c5b--------------------------------) ·阅读时间：9 分钟·2024 年 4 月 22 日

--

![](img/15f326cb5e620cc41b19938a5438d528.png)

在这篇文章中，我们将讨论部分内容，包括“服务基础设施”和“流程管理”。图片改编自 Sculley 等人（2015）中的图 1 *[1]*

MLOps（机器学习运维）是一个涵盖复杂任务、流程和基础设施的术语，旨在搭建、自动化和监控。谷歌在 2015 年早期发布的一篇论文（在这一术语诞生之前）很好地描述了这一点，指出机器学习代码只占整个实际机器学习系统的一个小部分 [1]。

在本文中，我将分享 MLOps 的一个组成部分，展示如何将容器化工作负载或模型部署到服务基础设施，同时考虑到自动化、安全性和反馈回路。

您可以选择许多不同的平台和软件进行部署，而我所展示的只是其中之一。虽然文中使用的 AWS、Terraform、GitLab 和 Ansible 在现实世界中被广泛采用，但它们也有一些流行的替代品。然而，方法论和流程是中立的，也可以为您如何在其他平台上实施提供一些见解。

# 目录

架构概述

创建一个 Ansible Playbook

提供云资源 构建一个 GitLab-CI 流水线
