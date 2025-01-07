# 数据工程基础设施指南

> 原文：[`towardsdatascience.com/a-guide-to-data-engineering-infrastructure-cb074e0d3f99?source=collection_archive---------5-----------------------#2024-01-20`](https://towardsdatascience.com/a-guide-to-data-engineering-infrastructure-cb074e0d3f99?source=collection_archive---------5-----------------------#2024-01-20)

## 使用现代工具自动化资源配置

[](https://mshakhomirov.medium.com/?source=post_page---byline--cb074e0d3f99--------------------------------)![💡Mike Shakhomirov](https://mshakhomirov.medium.com/?source=post_page---byline--cb074e0d3f99--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cb074e0d3f99--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cb074e0d3f99--------------------------------) [💡Mike Shakhomirov](https://mshakhomirov.medium.com/?source=post_page---byline--cb074e0d3f99--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cb074e0d3f99--------------------------------) ·12 分钟阅读·2024 年 1 月 20 日

--

![](img/9cc6d3f789ddc67f30dece40ff6ce671.png)

图片来源：[Ehud Neuhaus](https://unsplash.com/@paramir?utm_source=medium&utm_medium=referral) 由 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral) 提供

现代数据栈由各种工具和框架组成，用于处理数据。通常，它是一个庞大的云资源集合，旨在转换数据并将其带到可以生成数据洞察的状态。管理这些数据处理资源的庞大数量并非易事，可能会让人感到不知所措。幸运的是，数据工程师发明了一个叫做**基础设施即代码**的解决方案。它本质上是帮助我们部署、配置和管理在数据管道中可能需要的所有资源的代码。在这个故事中，我想讨论一些流行的技术和现有框架，旨在简化资源配置和数据管道部署。我还记得在数据职业生涯的最初，我是通过网页用户界面部署数据资源的，比如存储桶、安全角色等。那些日子早已过去，但我仍然记得，当我了解到可以通过模板和代码编程方式完成时，那份喜悦和兴奋。

## 现代数据栈

什么是现代数据栈（MDS）？它是专门用于组织、存储和操作数据的技术，这些技术组成了现代数据栈[1]。这就是……
