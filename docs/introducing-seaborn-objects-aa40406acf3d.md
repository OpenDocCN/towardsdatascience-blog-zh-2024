# 介绍 Seaborn 对象

> 原文：[`towardsdatascience.com/introducing-seaborn-objects-aa40406acf3d?source=collection_archive---------2-----------------------#2024-03-17`](https://towardsdatascience.com/introducing-seaborn-objects-aa40406acf3d?source=collection_archive---------2-----------------------#2024-03-17)

## 快速成功的数据科学

## 一个绘图之环统治所有！

[](https://medium.com/@lee_vaughan?source=post_page---byline--aa40406acf3d--------------------------------)![Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--aa40406acf3d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--aa40406acf3d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--aa40406acf3d--------------------------------) [Lee Vaughan](https://medium.com/@lee_vaughan?source=post_page---byline--aa40406acf3d--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--aa40406acf3d--------------------------------) ·阅读时长 8 分钟·2024 年 3 月 17 日

--

![](img/627f6378ac73688cf9d177d0de7addcb.png)

一个环统治所有的图形（由 Dall-E2 创作）

你是否已经开始使用新的*Seaborn 对象系统*进行 Python 绘图了？你肯定应该开始使用；它是一个非常棒的工具。

新系统于 2022 年底推出，基于*图形语法*范式，这一范式也支持*Tableau*和 R 的*ggplot2*。这使得它更灵活、更模块化、更直观。使用 Python 绘图从未如此出色。

在这个*快速成功的数据科学*项目中，你将获得新系统基础的快速入门教程。你还将获得从 Seaborn 对象官方文档汇编的几个实用备忘单。

# 安装库

我们将在这个项目中使用以下开源库：[pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/install.html)，[Matplotlib](https://matplotlib.org/stable/users/installing/index.html)，和[seaborn](https://seaborn.pydata.org/installing.html)。你可以在每个超链接中找到安装说明。我推荐你在一个[虚拟环境](https://docs.python.org/3/library/venv.html)中安装这些库，或者如果你是 Anaconda 用户，在一个专门为这个项目创建的[conda 环境](https://www.geeksforgeeks.org/set-up-virtual-environment-for-python-using-anaconda/)中安装。

# 新的 Seaborn 对象接口

Seaborn 的目标一直是让 Matplotlib——Python 的主要绘图库——更易于使用并且更美观。作为这一目标的一部分，Seaborn 依赖于[*声明式*](https://medium.com/towards-data-science/declarative-vs-imperative-plotting-3ee9952d6bf3)...
