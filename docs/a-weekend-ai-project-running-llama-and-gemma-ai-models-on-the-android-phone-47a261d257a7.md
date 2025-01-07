# 一个周末 AI 项目：在 Android 手机上运行 LLaMA 和 Gemma AI 模型

> 原文：[`towardsdatascience.com/a-weekend-ai-project-running-llama-and-gemma-ai-models-on-the-android-phone-47a261d257a7?source=collection_archive---------6-----------------------#2024-03-09`](https://towardsdatascience.com/a-weekend-ai-project-running-llama-and-gemma-ai-models-on-the-android-phone-47a261d257a7?source=collection_archive---------6-----------------------#2024-03-09)

## 使用 C++和 Python 运行 LLaMA 和 Gemma 大语言模型

[](https://dmitryelj.medium.com/?source=post_page---byline--47a261d257a7--------------------------------)![Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--47a261d257a7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--47a261d257a7--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--47a261d257a7--------------------------------) [Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--47a261d257a7--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--47a261d257a7--------------------------------) ·7 分钟阅读·2024 年 3 月 9 日

--

![](img/9b7dee53944f0c0f84ecbb551767801a.png)

图片来源：Magnet.me，[Unsplash](https://unsplash.com/@magnetme)

现如今，“移动 AI”是一个快速增长的趋势。智能手机变得越来越强大，大型模型变得更加高效。一些用户可能会选择等待手机制造商添加新功能，但我们能否自己使用最新的 AI 模型呢？确实，我们可以，而且结果非常有趣。在本文中，我将展示如何在 Android 手机上运行 LLaMA 和 Gemma 大型语言模型，我们将看到它是如何工作的。像我所有的测试一样，所有的模型都将在本地运行，不需要使用云 API 或支付。

让我们开始吧！

## Termux

我们测试的第一个组件是[Termux](https://wiki.termux.com/wiki/Main_Page)，这是一个作为 Android 应用程序制作的完整 Linux 终端。它是免费的，并且不需要 root 权限；所有的 Linux 组件都仅在 Termux 文件夹中运行。Termux 可以从[Google Play](https://play.google.com/store/apps/details?id=com.termux)下载，但在撰写本文时，那个版本已经比较旧，并且 Termux 中的“pkg update”命令不再工作了。一个更新版本可以在[F-Droid](https://f-droid.org/en/packages/com.termux/)网站上找到；它运行良好，我没有遇到任何问题。

当 Termux 安装在手机上时，我们可以运行它并看到一个标准的 Linux 命令行界面：
