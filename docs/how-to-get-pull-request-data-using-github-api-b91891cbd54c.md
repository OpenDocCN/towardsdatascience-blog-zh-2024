# 如何使用 GitHub API 获取拉取请求数据

> 原文：[https://towardsdatascience.com/how-to-get-pull-request-data-using-github-api-b91891cbd54c?source=collection_archive---------3-----------------------#2024-09-29](https://towardsdatascience.com/how-to-get-pull-request-data-using-github-api-b91891cbd54c?source=collection_archive---------3-----------------------#2024-09-29)

## 获取任意两次提交之间的差异

[](https://sonery.medium.com/?source=post_page---byline--b91891cbd54c--------------------------------)[![Soner Yıldırım](../Images/c589572e9d1ee176cd4f5a0008173f1b.png)](https://sonery.medium.com/?source=post_page---byline--b91891cbd54c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b91891cbd54c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b91891cbd54c--------------------------------) [Soner Yıldırım](https://sonery.medium.com/?source=post_page---byline--b91891cbd54c--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b91891cbd54c--------------------------------) ·阅读时间 5 分钟·2024年9月29日

--

![](../Images/3b97d7d9bf4b8ff4610839f9d49b67b8.png)

[照片由 Bofu Shaw](https://unsplash.com/@hikeshaw?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来自 [Unsplash](https://unsplash.com/photos/three-white-and-red-ceramic-owl-figurines-ntdr0AMZdvw?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

GitHub 就像是代码的维基百科。GitHub 上的所有内容不能轻易假定，但它包含了如何创建一些最佳软件工具的精华和历史。

如果没有一个用于访问如此宝贵资源的 API，那将是非常遗憾的。幸运的是，我们确实有一个，令人惊讶的是，它叫做 GitHub API。

让我首先说明一下本文不讨论的内容。我们不会讨论 git 注释或如何在软件开发中使用 git。

本文更多地是关于如何使用 GitHub API 进行分析。分析的首要要求是数据，而 GitHub 拥有大量数据。

我们可以通过 GitHub API 获取的信息量和种类简直令人惊叹。此外，它是一个维护良好且有详细文档的 API，所以我们不会遇到太多困难来获取所需的信息。

我们可以通过 GitHub API 获取很多数据，例如：

+   每个拉取请求的提交数量

+   仓库的文件夹和文件结构

+   每次提交编辑的文件平均数量

+   基于开发者的数据，例如谁在上个月推送了最多的提交

+   基于文件的数据，例如…
