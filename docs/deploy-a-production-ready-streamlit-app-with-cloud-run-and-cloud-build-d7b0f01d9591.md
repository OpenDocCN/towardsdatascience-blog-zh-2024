# 使用 Cloud Run 和 Cloud Build 部署生产就绪的 Streamlit 应用

> 原文：[https://towardsdatascience.com/deploy-a-production-ready-streamlit-app-with-cloud-run-and-cloud-build-d7b0f01d9591?source=collection_archive---------5-----------------------#2024-03-22](https://towardsdatascience.com/deploy-a-production-ready-streamlit-app-with-cloud-run-and-cloud-build-d7b0f01d9591?source=collection_archive---------5-----------------------#2024-03-22)

## 如何在无服务器架构和 CI/CD 流水线中部署容器化应用。

[](https://ahmedbesbes.medium.com/?source=post_page---byline--d7b0f01d9591--------------------------------)[![Ahmed Besbes](../Images/93804d9291439715e578f204b79c9bdd.png)](https://ahmedbesbes.medium.com/?source=post_page---byline--d7b0f01d9591--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d7b0f01d9591--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d7b0f01d9591--------------------------------) [Ahmed Besbes](https://ahmedbesbes.medium.com/?source=post_page---byline--d7b0f01d9591--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d7b0f01d9591--------------------------------) ·8分钟阅读·2024年3月22日

--

![](../Images/c52ef8a1606ef2f17afb9cc0f2929540.png)

图片来自 [Dominik Lückmann](https://unsplash.com/@exdigy?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

如果你是数据科学家，可能已经熟悉 [Streamlit](https://streamlit.io/)，你可能曾用它来原型设计一个演示、分享仪表盘，甚至构建一个更复杂的应用。

在这篇文章中，你将学习如何在 Google Cloud Platform 上快速且高效地部署 Streamlit 应用。

为此，你需要：

+   一个有效的 GCP 账户和信用卡（尽管按照本教程操作不会花费你一分钱）

+   [Pulumi](https://www.pulumi.com/docs/): 一款基础设施即代码（IaC）工具，用于配置部署应用所需的云资源

+   需要一些 Google Cloud 服务（Cloud Run）、Docker 和 Poetry 的基础知识

+   一个 Github 账户，用于通过 Cloud Build CI/CD 平台触发自动部署

如果你不熟悉这些工具，下面会详细介绍，并提供链接让你了解更多。

不再赘述，接下来让我们一探究竟 👇

# 什么是 Cloud Run？☁️
