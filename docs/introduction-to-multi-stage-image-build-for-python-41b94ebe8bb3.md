# Python 的多阶段镜像构建简介

> 原文：[`towardsdatascience.com/introduction-to-multi-stage-image-build-for-python-41b94ebe8bb3?source=collection_archive---------0-----------------------#2024-04-21`](https://towardsdatascience.com/introduction-to-multi-stage-image-build-for-python-41b94ebe8bb3?source=collection_archive---------0-----------------------#2024-04-21)

## 本文介绍了多阶段构建方法，用于设置轻量级的 Python 开发环境。

[](https://medium.com/@rami.krispin?source=post_page---byline--41b94ebe8bb3--------------------------------)![Rami Krispin](https://medium.com/@rami.krispin?source=post_page---byline--41b94ebe8bb3--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--41b94ebe8bb3--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--41b94ebe8bb3--------------------------------) [Rami Krispin](https://medium.com/@rami.krispin?source=post_page---byline--41b94ebe8bb3--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--41b94ebe8bb3--------------------------------) ·阅读时间 9 分钟·2024 年 4 月 21 日

--

在我开始使用容器将代码部署到 Github Actions 时，我从未过多关注我的镜像大小。这里的数学原理很简单：容器的大小越大，加载时间越长，因此**成本也越高**。当我的 Python 镜像大小达到了 5GB（感谢 PyTorch！）时，我开始探索更高效的镜像构建方法。

**简而言之——我通过多阶段构建方法将基线镜像的大小减少了 65%。**

在这篇文章中，我们将回顾三种构建方法，看看通过几个简单的步骤，如何减少 Python 镜像的大小。我们将从使用官方 Python 镜像`python:3.1o`的基线构建开始，然后探索镜像精简版本，最后介绍一种更高级的方法——多阶段构建。

![](img/e14512b77e9e196a3d0a100b457d8484.png)

一艘满载集装箱的货船（由作者使用 Midjourney 制作）

# 先决条件

要跟随本教程，你将需要以下设置：

+   Docker Desktop（或等效工具），如果你使用的是 macOS 或 Windows 操作系统，或者如果你使用的是 Linux 系统，则需要安装 Docker...
