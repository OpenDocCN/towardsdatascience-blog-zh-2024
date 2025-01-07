# 在 AWS EC2 上部署 Tiny-Llama

> 原文：[https://towardsdatascience.com/deploy-tiny-llama-on-aws-ec2-f3ff312c896d?source=collection_archive---------3-----------------------#2024-01-12](https://towardsdatascience.com/deploy-tiny-llama-on-aws-ec2-f3ff312c896d?source=collection_archive---------3-----------------------#2024-01-12)

![](../Images/f413377810614e94c713c29a27c88010.png)

Tiny-Llama logo（来源：[https://github.com/jzhang38/TinyLlama](https://github.com/jzhang38/TinyLlama)）

## 学习如何使用 AWS 和 FastAPI 部署一个真实的 ML 应用

[](https://medium.com/@marcellopoliti?source=post_page---byline--f3ff312c896d--------------------------------)[![Marcello Politi](../Images/484e44571bd2e75acfe5fef3146ab3c2.png)](https://medium.com/@marcellopoliti?source=post_page---byline--f3ff312c896d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f3ff312c896d--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f3ff312c896d--------------------------------) [Marcello Politi](https://medium.com/@marcellopoliti?source=post_page---byline--f3ff312c896d--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f3ff312c896d--------------------------------) ·13 分钟阅读·2024年1月12日

--

## 介绍

我一直认为，即使是世界上最好的项目，如果人们不能使用它，它也没有太大价值。这就是为什么学习如何部署机器学习模型非常重要的原因。在本文中，我们将重点介绍如何在名为 EC2 的 AWS 实例上部署一个小型的大型语言模型——Tiny-Llama。

我为这个项目使用的工具列表：

+   [Deepnote](https://deepnote.com/): 是一个基于云的笔记本，非常适合协作数据科学项目，适合原型设计

+   [FastAPI](https://fastapi.tiangolo.com/): 是一个用于构建 Python API 的 Web 框架

+   [AWS EC2](https://aws.amazon.com/pm/ec2/?gclid=CjwKCAiAqNSsBhAvEiwAn_tmxcAUKn_yNi6eSU_EJAZAV5z3kUQ4SzQmrv-xK9Jj0xkFLd9ahM84ihoCljcQAvD_BwE&trk=be8ffa73-d198-48a9-8728-2fdd4b8a06a0&sc_channel=ps&ef_id=CjwKCAiAqNSsBhAvEiwAn_tmxcAUKn_yNi6eSU_EJAZAV5z3kUQ4SzQmrv-xK9Jj0xkFLd9ahM84ihoCljcQAvD_BwE%3AG%3As&s_kwcid=AL%214422%213%21494972673739%21e%21%21g%21%21aws+ec2%2112196406589%21115425120925): 是一种提供可观计算能力的云计算服务

+   [Nginx](https://nginx.org/en/): 是一个 HTTP 和反向代理服务器。我用它将 FastAPI 服务器连接到 AWS

+   [GitHub](https://github.com/): GitHub 是一个软件项目的托管服务

+   [HuggingFace](https://huggingface.co/): 是一个托管和协作无限模型、数据集和应用的平台。

## 关于 Tiny Llama
