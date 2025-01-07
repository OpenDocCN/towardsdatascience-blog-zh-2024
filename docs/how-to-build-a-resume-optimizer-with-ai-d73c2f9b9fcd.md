# 如何构建一个 AI 简历优化工具

> 原文：[https://towardsdatascience.com/how-to-build-a-resume-optimizer-with-ai-d73c2f9b9fcd?source=collection_archive---------3-----------------------#2024-12-30](https://towardsdatascience.com/how-to-build-a-resume-optimizer-with-ai-d73c2f9b9fcd?source=collection_archive---------3-----------------------#2024-12-30)

## 逐步指南和示例 Python 代码

[](https://shawhin.medium.com/?source=post_page---byline--d73c2f9b9fcd--------------------------------)[![Shaw Talebi](../Images/1449cc7c08890e2078f9e5d07897e3df.png)](https://shawhin.medium.com/?source=post_page---byline--d73c2f9b9fcd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d73c2f9b9fcd--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d73c2f9b9fcd--------------------------------) [Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--d73c2f9b9fcd--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d73c2f9b9fcd--------------------------------) ·6分钟阅读·6天前

--

在之前的一篇博客文章中，我分享了[你可以在这个周末构建的 5 个 AI 项目](/5-ai-projects-you-can-build-this-weekend-with-python-c57724e9c461)，其中第一个项目的创意是简历优化工具。自那以后，许多人请求更多关于如何实现这个项目的指导。在本文中，我将通过一个使用 Python 和 OpenAI API 的示例实现来讲解这个过程。

![](../Images/a56334f7596475f2edcc9ccbd8547a75.png)

图片来自 Canva。

根据不同的职位描述调整你的简历是找工作的一个有效但繁琐的步骤。即使你已经为某个特定职位定制了简历，企业可能对类似职位名称有**不同的期望**。

例如，以下是 Netflix 和 Google 的两个相似数据科学家职位，它们的职位描述略有不同。幸运的是，借助今天的 AI 工具，**我们可以构建一个应用程序来简化这个过程**。

![](../Images/27c7d90ad47f642289b7945dba38030a.png)

相似数据科学家职位的对比。图像由作者提供。

# **我不能使用 ChatGPT 吗？**
