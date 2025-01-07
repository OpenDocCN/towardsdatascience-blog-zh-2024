# 使用VSCode在Python中创建项目环境

> 原文：[https://towardsdatascience.com/creating-project-environments-in-python-with-vscode-b95b530cd627?source=collection_archive---------4-----------------------#2024-09-13](https://towardsdatascience.com/creating-project-environments-in-python-with-vscode-b95b530cd627?source=collection_archive---------4-----------------------#2024-09-13)

## 学习如何管理你的Python项目的不同环境

[](https://gustavorsantos.medium.com/?source=post_page---byline--b95b530cd627--------------------------------)[![Gustavo R Santos](../Images/a19a9f4525cdeb6e7a76cd05246aa622.png)](https://gustavorsantos.medium.com/?source=post_page---byline--b95b530cd627--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b95b530cd627--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b95b530cd627--------------------------------) [Gustavo R Santos](https://gustavorsantos.medium.com/?source=post_page---byline--b95b530cd627--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b95b530cd627--------------------------------) ·5分钟阅读·2024年9月13日

--

![](../Images/72dbc2094f3f48c1b20d70bd4deab7a6.png)

图片由 [jesse orrico](https://unsplash.com/@jessedo81?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 提供，来自 [Unsplash](https://unsplash.com/photos/gray-metal-drawers-h6xNSDlgciU?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

# 介绍

创建数据科学项目可以非常简单。现在有很多可用的资源，选择一个开发工具并启动项目就能轻松开始。

文档也很容易获得，此外，还有多个AI机器人可以帮助你完成几乎所有想要创建的内容。

然而，随着项目变得越来越复杂和专业，我们需要开始将项目彼此隔离。有时，在项目A中一起运行良好的模块，可能在项目B中无法正常运行。或者两个不同包中具有相同名称的方法可能会引起混淆。我的意思是，在非隔离的环境中，很多事情可能会发生。

这时，我们就会发现需要开始隔离开发环境。因此，在这篇文章中，目的是向你展示一种快速简便的方法，使用Python和VS Code创建一个隔离的环境。

让我们开始吧。

# 项目环境

如前所述，开发环境是一个在计算机内部创建的隔离“盒子”…
