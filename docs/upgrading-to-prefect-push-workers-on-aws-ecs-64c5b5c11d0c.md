# 升级到 AWS ECS 上的 Prefect 推送工作者

> 原文：[https://towardsdatascience.com/upgrading-to-prefect-push-workers-on-aws-ecs-64c5b5c11d0c?source=collection_archive---------7-----------------------#2024-10-12](https://towardsdatascience.com/upgrading-to-prefect-push-workers-on-aws-ecs-64c5b5c11d0c?source=collection_archive---------7-----------------------#2024-10-12)

## 从 Prefect 2.0 升级到 3.0，并使用新的推送工作池，大大简化基础设施管理并降低成本。

[](https://medium.com/@broepke?source=post_page---byline--64c5b5c11d0c--------------------------------)[![Brian Roepke](../Images/0b7ef72cbfc9acda69fde14127d65dcf.png)](https://medium.com/@broepke?source=post_page---byline--64c5b5c11d0c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--64c5b5c11d0c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--64c5b5c11d0c--------------------------------) [Brian Roepke](https://medium.com/@broepke?source=post_page---byline--64c5b5c11d0c--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--64c5b5c11d0c--------------------------------)·6 分钟阅读·2024 年 10 月 12 日

--

![](../Images/e31c44bdde96250c4464aa6a0f992c5c.png)

照片由[Techivation](https://unsplash.com/@techivation?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 概述

[Prefect](https://www.prefect.io/)是一个用于数据工程的开源**编排**工具。这个基于 Python 的工具允许你**定义**、**调度**和**监控**你的数据管道。Prefect 是一个适用于希望自动化他们的**数据管道**的数据工程师和科学家的绝佳工具。

在一篇名为[Getting Started with Prefect: Powerful Orchestration for Your Data](https://medium.com/@broepke/getting-started-with-prefect-powerful-orchestration-for-your-data-20ef827b20d5)的文章中，我写到部署 Prefect 到 AWS EC2 来运行他们所谓的代理，本质上是在启动后运行作业。

升级到[版本 3.0](https://www.prefect.io/blog/introducing-prefect-3-0)，Prefect正在淘汰长期存在的代理模型，转而采用一种称为工作者的新架构。从 v2 升级到 v3 立即破坏了我的部署，让我不得不想出一个替代方案。

工作者与他们运行的基础设施更紧密地集成在一起。这是一个了不起的改进，因为在过去，你需要弄清楚如何在你的计算机上托管代理。这就像在经典托管服务器上安装软件并维护操作系统更新一样。
