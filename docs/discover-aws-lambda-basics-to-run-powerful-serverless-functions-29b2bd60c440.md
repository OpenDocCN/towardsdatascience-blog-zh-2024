# 发现 AWS Lambda 基础，运行强大的无服务器函数

> 原文：[https://towardsdatascience.com/discover-aws-lambda-basics-to-run-powerful-serverless-functions-29b2bd60c440?source=collection_archive---------6-----------------------#2024-10-07](https://towardsdatascience.com/discover-aws-lambda-basics-to-run-powerful-serverless-functions-29b2bd60c440?source=collection_archive---------6-----------------------#2024-10-07)

## 学习我如何首次设置 AWS Lambda

[](https://oieivind.medium.com/?source=post_page---byline--29b2bd60c440--------------------------------)[![Eivind Kjosbakken](../Images/5f91b74428e1202fc4a176a3dd1cb1c7.png)](https://oieivind.medium.com/?source=post_page---byline--29b2bd60c440--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--29b2bd60c440--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--29b2bd60c440--------------------------------) [Eivind Kjosbakken](https://oieivind.medium.com/?source=post_page---byline--29b2bd60c440--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--29b2bd60c440--------------------------------) ·阅读时长10分钟·2024年10月7日

--

本文带你走过我如何开始使用 AWS Lambda 的过程。文章旨在展示如何设置 AWS Lambda 函数，并且展示我解决问题的方法，如何首次设置 Lambda 函数。希望这能帮助你了解如何处理计算机科学中的新问题，并找到解决方案。能够自己解决问题是编程中的一项关键技能，培养这项技能是本文的主要动机之一。

![](../Images/7a238f56fb1bd1f3d4359db5760b7c48.png)

本教程将展示如何设置 AWS Lambda 函数，同时也会介绍我首次解决如何完成这一任务的问题的方式。图片由 ChatGPT 提供。

# 动机

我编写本教程以及未来类似教程的动机，是学习对我作为数据科学家来说非常重要的新概念。作为数据科学家，持续更新知识是至关重要的。我意识到自己对将函数部署到云端的理解有限，而这对于例如想要托管机器学习模型的人来说非常重要。因此，我决定学习如何使用 AWS Lambda 函数来部署这些函数。另一个本教程的动机是展示我的问题解决方法，因为我将要处理一个自己几乎没有预先了解的任务。本文应该会…
