# 通过这 10 个有用的技巧，提升你的 Git 知识，浏览 Git 历史记录

> 原文：[`towardsdatascience.com/level-up-your-git-knowledge-with-these-10-useful-tips-to-browse-git-history-5f7b9f4b0e1d?source=collection_archive---------8-----------------------#2024-03-09`](https://towardsdatascience.com/level-up-your-git-knowledge-with-these-10-useful-tips-to-browse-git-history-5f7b9f4b0e1d?source=collection_archive---------8-----------------------#2024-03-09)

![](img/c4d8a1144330e2ba0125bb441ad9d14b.png)

图片来源：[Thomas Kelley](https://unsplash.com/@thkelley?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 精通 Git

## 学习高级的 git log

[](https://zluvsand.medium.com/?source=post_page---byline--5f7b9f4b0e1d--------------------------------)![Zolzaya Luvsandorj](https://zluvsand.medium.com/?source=post_page---byline--5f7b9f4b0e1d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--5f7b9f4b0e1d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5f7b9f4b0e1d--------------------------------) [Zolzaya Luvsandorj](https://zluvsand.medium.com/?source=post_page---byline--5f7b9f4b0e1d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--5f7b9f4b0e1d--------------------------------) ·阅读时长 10 分钟·2024 年 3 月 9 日

--

如果你已经在使用 Git，那么你可能熟悉 `git log` 命令。除了它的基本用法（即普通的 `git log`），这个命令的高级用法非常强大，可以使得浏览仓库历史记录变得更加顺畅和富有信息。在这篇文章中，我们将学习一些使用 `git log` 的实用方法，帮助你将 Git 知识提升到一个新水平。

![](img/b5748d323b495713fb376aa7fcaeddcb.png)

图片来源：[Chris Lawton](https://unsplash.com/@chrislawton?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 🔧 0\. 设置

> 本文假设读者（即你）已经熟悉 Git 的基本用法。如果你需要复习 Git 的基础知识，可以先阅读这篇文章。为了更好地理解本文，我建议你在阅读过程中动手实践这些命令。我们在积极练习新知识时，比被动阅读更能快速学习。

我们将使用我最喜欢的 GitHub 仓库之一：[ABSphreak/readme-jokes: 😄 适用于你的 GitHub README 文件的笑话](https://github.com/ABSphreak/readme-jokes) 来演示命令的使用。这个精彩的轻量级仓库让我可以在我的 GitHub 个人资料中添加随机的编程笑话。让我们开始吧……
