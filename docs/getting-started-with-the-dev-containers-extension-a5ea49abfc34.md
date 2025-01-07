# Dev Containers 扩展入门

> 原文：[https://towardsdatascience.com/getting-started-with-the-dev-containers-extension-a5ea49abfc34?source=collection_archive---------9-----------------------#2024-05-07](https://towardsdatascience.com/getting-started-with-the-dev-containers-extension-a5ea49abfc34?source=collection_archive---------9-----------------------#2024-05-07)

## 这是一个逐步教程，帮助您入门 Dev Containers 扩展。本教程是一个系列教程的第一部分，重点介绍 Dev Containers 扩展。

[](https://medium.com/@rami.krispin?source=post_page---byline--a5ea49abfc34--------------------------------)[![Rami Krispin](../Images/8af28c282d42a2a27c28aa8af4c8d482.png)](https://medium.com/@rami.krispin?source=post_page---byline--a5ea49abfc34--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a5ea49abfc34--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a5ea49abfc34--------------------------------) [Rami Krispin](https://medium.com/@rami.krispin?source=post_page---byline--a5ea49abfc34--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a5ea49abfc34--------------------------------) ·阅读时间 9 分钟·2024年5月7日

--

我完全将开发工作流转移到 Visual Studio Code（VScode）的主要原因之一是 [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) 扩展。Dev Containers 扩展为 VScode 提供了 Docker 和容器的原生集成。它减少了设置 Docker 化环境以及在使用容器框架时与他人协作的工作量。本教程重点介绍如何“入门”使用 Dev Containers 扩展。我们将深入探讨在您的开发工作流中使用 Dev Containers 扩展（和 Docker）的动机。此外，我们还将回顾将 Dev Container [模板](https://containers.dev/templates) 添加到项目并在容器中启动它的步骤。

在接下来的教程中，我们将探索该扩展的核心功能，并回顾定制化选项。

![](../Images/7f654de22acb5763c546eae9b915c8af.png)

一艘货船停靠在港口（由作者使用 Midjourney 创建）

本教程结束时，您将了解如何：

+   安装 Dev Containers 扩展

+   将 Dev Containers 设置文件添加到项目中

+   在一个…中启动项目
