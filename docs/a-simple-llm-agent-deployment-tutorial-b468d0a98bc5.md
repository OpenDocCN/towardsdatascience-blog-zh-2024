# 简单的LLM代理部署教程

> 原文：[https://towardsdatascience.com/a-simple-llm-agent-deployment-tutorial-b468d0a98bc5?source=collection_archive---------5-----------------------#2024-11-05](https://towardsdatascience.com/a-simple-llm-agent-deployment-tutorial-b468d0a98bc5?source=collection_archive---------5-----------------------#2024-11-05)

## 简单、可扩展且快速的LLM代理部署模板

[](https://medium.com/@CVxTz?source=post_page---byline--b468d0a98bc5--------------------------------)[![Youness Mansar](../Images/b68fe2cbbe219ab0231922c7165f2b6a.png)](https://medium.com/@CVxTz?source=post_page---byline--b468d0a98bc5--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b468d0a98bc5--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b468d0a98bc5--------------------------------) [Youness Mansar](https://medium.com/@CVxTz?source=post_page---byline--b468d0a98bc5--------------------------------)

·发布在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b468d0a98bc5--------------------------------) ·阅读时间：8分钟·2024年11月5日

--

![](../Images/e15c31715403c3e70027957010e9b7b1.png)

图片由[Jeremy Bishop](https://unsplash.com/@jeremybishop?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

许多教程展示了如何实现LLM代理。然而，关于将这些代理部署到API或用户友好的UI后面的资源非常有限。本文通过一步步的指南来填补这一空白，介绍如何实现和部署一个简单但功能齐全的LLM代理。这为您的LLM代理概念验证提供了一个起点，无论是用于个人使用还是与他人共享。

我们的实现分为几个部分：

1.  **代理实现：** 使用LangGraph作为代理框架，Fireworks AI作为LLM服务。

1.  **用户界面：** 通过使用FastAPI和NiceGUI构建的UI来暴露代理。

1.  **容器化：** 将应用打包为Docker镜像。

1.  **部署：** 将Docker镜像部署到Google Cloud Run。

完整的代码和演示应用将在文章末尾提供链接。

![](../Images/c88264298a5c9b5e356b213deb8899a1.png)

组件列表 — 图片来自作者

# 构建代理

该代理需要两个核心组件：

+   **LLM服务：** 我们将使用[Fireworks AI](https://fireworks.ai/)。
