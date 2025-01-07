# 配置 Pytest 以运行 Doctest

> 原文：[https://towardsdatascience.com/configuring-pytest-to-run-doctest-a4e6b2821043?source=collection_archive---------8-----------------------#2024-04-02](https://towardsdatascience.com/configuring-pytest-to-run-doctest-a4e6b2821043?source=collection_archive---------8-----------------------#2024-04-02)

## PYTHON 编程

## 学习如何在 pyproject.toml 中配置 pytest 以运行 doctests

[](https://medium.com/@nyggus?source=post_page---byline--a4e6b2821043--------------------------------)[![Marcin Kozak](../Images/d7faf62e48ed81dab5d8ad92819fff54.png)](https://medium.com/@nyggus?source=post_page---byline--a4e6b2821043--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--a4e6b2821043--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--a4e6b2821043--------------------------------) [Marcin Kozak](https://medium.com/@nyggus?source=post_page---byline--a4e6b2821043--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--a4e6b2821043--------------------------------) ·10分钟阅读·2024年4月2日

--

![](../Images/ee4a6fb822fa87ee6cc352b66ad225c4.png)

由 [Paul Hanaoka](https://unsplash.com/@plhnk?utm_source=medium&utm_medium=referral) 提供的照片，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

现代 Python 项目通过 `pyproject.toml` 文件进行管理。你可以用它来管理常规项目和 Python 包，这使得该文件成为设置各种类型 Python 项目的通用工具。

`pyproject.toml` 文件可以包含你项目所需的一切内容，例如：

+   元数据，如项目名称、版本、作者、许可证、分类器、网址和描述（包括简短描述和长描述）。

+   开发环境和生产环境的配置，即依赖项和可选依赖项。

+   开发工具的配置，如 `[black](https://github.com/psf/black)`、`[ruff](https://docs.astral.sh/ruff/)`、`[pylint](https://github.com/pylint-dev/pylint)`、`[pytest](https://docs.pytest.org/)` 和许多其他工具。

当项目变大时，你可以将大部分信息移到其他配置文件中，如 `pytest.ini`、`requirements.txt`、`requirements_dev.txt` 等等。但当 `pyproject.toml` 文件不会变得过长时，我会将所有信息保存在里面——这样可以保持项目根目录的简洁。

这只是关于 `pyproject.toml` 文件的基本信息。如果你想了解更多关于如何使用它的内容，可以从这里开始：
