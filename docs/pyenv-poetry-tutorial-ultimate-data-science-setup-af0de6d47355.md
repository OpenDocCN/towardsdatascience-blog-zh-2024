# PyEnv & Poetry 教程：终极数据科学设置

> 原文：[https://towardsdatascience.com/pyenv-poetry-tutorial-ultimate-data-science-setup-af0de6d47355?source=collection_archive---------1-----------------------#2024-07-15](https://towardsdatascience.com/pyenv-poetry-tutorial-ultimate-data-science-setup-af0de6d47355?source=collection_archive---------1-----------------------#2024-07-15)

## 如何在数据科学项目中使用 PyEnv 和 Poetry 一起进行环境和包管理

[](https://medium.com/@egorhowell?source=post_page---byline--af0de6d47355--------------------------------)[![Egor Howell](../Images/1f796e828f1625440467d01dcc3e40cd.png)](https://medium.com/@egorhowell?source=post_page---byline--af0de6d47355--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--af0de6d47355--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--af0de6d47355--------------------------------) [Egor Howell](https://medium.com/@egorhowell?source=post_page---byline--af0de6d47355--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--af0de6d47355--------------------------------) ·8分钟阅读·2024年7月15日

--

![](../Images/ebf815de64125576f9454add70ca1a17.png)

图片由[Danial Igdery](https://unsplash.com/@ricaros?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

我不是[**Anaconda**](https://www.anaconda.com/)的忠实粉丝。它是一个很好的工具，能够帮助你入门数据科学和 Python 生态系统，但我觉得它的安装过程太繁琐且具有侵入性。

这就是为什么我更喜欢使用[**PyEnv**](https://github.com/pyenv/pyenv)进行 Python 管理，使用[**Poetry**](https://python-poetry.org/)来管理依赖项。这个组合对我来说非常有效，因为它们都很轻量、相对较小，并且是易于理解的技术。

在本文中，我将为你提供一个简短的教程，教你如何开始在项目中使用 PyEnv 和 Poetry！

> **注意：** 本文将为 MacOS 和 Unix 用户提供教程，但 PyEnv 和 Poetry 也有适用于其他操作系统的指南。

# PyEnv

## 它是什么？

那么，什么是 PyEnv？它是 Python Environment（Python 环境）的缩写，是一个管理 Python 版本的工具。正如它们在[**GitHub 页面**](https://github.com/pyenv/pyenv)上所描述的：

> pyenv 让你可以轻松地在多个 Python 版本之间切换。它简单、不显眼，并遵循 UNIX 传统，专注于单一功能，做到这一点非常出色。
