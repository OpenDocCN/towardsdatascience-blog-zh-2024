# 使用 Hugging Face、FastAPI 和 Docker 的端到端 NLP 项目

> 原文：[`towardsdatascience.com/end-to-end-nlp-project-with-hugging-face-fastapi-and-docker-615a63d80c53?source=collection_archive---------2-----------------------#2024-03-07`](https://towardsdatascience.com/end-to-end-nlp-project-with-hugging-face-fastapi-and-docker-615a63d80c53?source=collection_archive---------2-----------------------#2024-03-07)

## 本教程解释了如何使用 Hugging Face、FastAPI 和 Docker 构建一个容器化的情感分析 API。

[](https://kaspergroesludvigsen.medium.com/?source=post_page---byline--615a63d80c53--------------------------------)![Kasper Groes Albin Ludvigsen](https://kaspergroesludvigsen.medium.com/?source=post_page---byline--615a63d80c53--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--615a63d80c53--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--615a63d80c53--------------------------------) [Kasper Groes Albin Ludvigsen](https://kaspergroesludvigsen.medium.com/?source=post_page---byline--615a63d80c53--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--615a63d80c53--------------------------------) ·阅读时间：10 分钟·2024 年 3 月 7 日

--

![](img/0cce1f0d3ed49f09a3eee64df608b20e.png)

图片由 [Joshua Hoehne](https://unsplash.com/@mrthetrain?utm_source=medium&utm_medium=referral) 提供，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

根据各类报告，许多 AI 项目失败了（例如，[哈佛商业评论](https://hbr.org/2023/11/keep-your-ai-projects-on-track)）。我推测，AI 项目成功的障碍部分来自于技术上的一个步骤，即从构建模型到使其广泛可用给你组织中的其他人之间的过渡。

那么，如何让你的模型便于他人使用呢？一种方法是将它封装成 API，并将其容器化，以便该模型能够在任何安装了 Docker 的服务器上公开。这正是我们在本教程中将要做的。

我们将从 Hugging Face 获取一个情感分析模型（这只是一个随意的选择，目的是选择一个容易作为示例展示的模型），编写一个 API 端点，通过 FastAPI 曝露该模型，然后使用 Docker 将我们的情感分析应用容器化。我会提供代码示例和解释。

本教程代码已在 Linux 上测试，应该也可以在 Windows 上运行。
