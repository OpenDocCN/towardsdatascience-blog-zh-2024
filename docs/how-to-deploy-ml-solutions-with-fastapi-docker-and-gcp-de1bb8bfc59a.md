# 如何使用 FastAPI、Docker 和 GCP 部署机器学习解决方案

> 原文：[`towardsdatascience.com/how-to-deploy-ml-solutions-with-fastapi-docker-and-gcp-de1bb8bfc59a?source=collection_archive---------3-----------------------#2024-06-05`](https://towardsdatascience.com/how-to-deploy-ml-solutions-with-fastapi-docker-and-gcp-de1bb8bfc59a?source=collection_archive---------3-----------------------#2024-06-05)

## 带有 Python 示例代码的实操指南

[](https://shawhin.medium.com/?source=post_page---byline--de1bb8bfc59a--------------------------------)![Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--de1bb8bfc59a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--de1bb8bfc59a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--de1bb8bfc59a--------------------------------) [Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--de1bb8bfc59a--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--de1bb8bfc59a--------------------------------) ·10 分钟阅读·2024 年 6 月 5 日

--

这是关于全栈数据科学的[更大系列文章](https://shawhin.medium.com/list/full-stack-data-science-f0910c75d006)中的第五篇文章。在本文中，我将介绍基于机器学习的搜索 API 的部署过程。虽然我们可以用无数种方式实现这一目标，但我在这里讨论了一种简单的三步方法，它可以应用于几乎任何机器学习解决方案。示例代码可以在[GitHub 仓库](https://github.com/ShawhinT/deploy-api-example)中免费获取。

![](img/a773b0982945e711c888cd938e025b04.png)

图片由[Andy Hermawan](https://unsplash.com/@kolamdigital?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

尽管机器学习看起来主要是训练复杂的模型，但在现实世界中，**一个模型（单独存在时）并不会产生价值**。要将机器学习模型转化为机器学习解决方案（即具有价值的东西），我们必须“部署它”。

这可以采取多种形式。例如，创建一个 Web 界面，用户可以与模型互动，将模型集成到现有的软件系统中，甚至仅仅为开发者设置一个 API 来访问该模型（想想 OpenAI 等等）。

我在这里描述的**三步策略**与所有这些示例兼容。它包括：

1.  创建推理 API（使用 FastAPI）

1.  将 API 容器化（通过 Docker）

1.  在云平台上运行容器（这里我使用 GCP）
