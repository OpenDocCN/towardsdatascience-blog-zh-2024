# 利用 Rust 的速度将 Python 库安装速度提高 100 倍

> 原文：[`towardsdatascience.com/use-rusts-speed-to-install-python-libraries-up-to-100-times-faster-f25489434337?source=collection_archive---------1-----------------------#2024-03-07`](https://towardsdatascience.com/use-rusts-speed-to-install-python-libraries-up-to-100-times-faster-f25489434337?source=collection_archive---------1-----------------------#2024-03-07)

![](img/ad19cc5a4f374530a297c7ecffc26d89.png)

使用 Midjourney 生成的图像

## `uv`的快速概述 — 这是 Python 包管理器中的新星。

[](https://eryk-lewinson.medium.com/?source=post_page---byline--f25489434337--------------------------------)![Eryk Lewinson](https://eryk-lewinson.medium.com/?source=post_page---byline--f25489434337--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f25489434337--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f25489434337--------------------------------) [Eryk Lewinson](https://eryk-lewinson.medium.com/?source=post_page---byline--f25489434337--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f25489434337--------------------------------) ·阅读时间 6 分钟 ·2024 年 3 月 7 日

--

对于数据科学家和 Python 程序员来说，`pip`不需要介绍。作为一个包管理器，它要么是首选解决方案，要么是我们在寻找最佳解决方案时的起点。

趣味提醒 🚨：虽然`pip`不需要介绍，但我最近才了解到，它其实代表着“Pip Installs Packages”或“Preferred Installer Program”。

`pip`并不是唯一的包管理器。下面是市场上最流行的一些工具：

+   `pip` — Python 的标准包管理器，Python 安装时默认包含。

+   `conda` — 一个专为数据科学设计的包和环境管理系统。除了 Python，它还可以安装其他语言编写的包（例如 R）。

+   `poetry` — 它旨在通过提供高效的依赖解析和虚拟环境等功能来简化 Python 的依赖管理。

+   `pipenv` — 这个工具将`pip`（用于安装包）与`virtualenv`（用于创建隔离环境）结合在一起。
