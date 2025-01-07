# 一个周末AI项目：在Android手机上运行LLaMA和Gemma AI模型

> 原文：[https://towardsdatascience.com/a-weekend-ai-project-running-llama-and-gemma-ai-models-on-the-android-phone-47a261d257a7?source=collection_archive---------6-----------------------#2024-03-09](https://towardsdatascience.com/a-weekend-ai-project-running-llama-and-gemma-ai-models-on-the-android-phone-47a261d257a7?source=collection_archive---------6-----------------------#2024-03-09)

## 使用C++和Python运行LLaMA和Gemma大语言模型

[](https://dmitryelj.medium.com/?source=post_page---byline--47a261d257a7--------------------------------)[![Dmitrii Eliuseev](../Images/7c48f0c016930ead59ddb785eaf3e0e6.png)](https://dmitryelj.medium.com/?source=post_page---byline--47a261d257a7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--47a261d257a7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--47a261d257a7--------------------------------) [Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--47a261d257a7--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--47a261d257a7--------------------------------) ·7分钟阅读·2024年3月9日

--

![](../Images/9b7dee53944f0c0f84ecbb551767801a.png)

图片来源：Magnet.me，[Unsplash](https://unsplash.com/@magnetme)

现如今，“移动AI”是一个快速增长的趋势。智能手机变得越来越强大，大型模型变得更加高效。一些用户可能会选择等待手机制造商添加新功能，但我们能否自己使用最新的AI模型呢？确实，我们可以，而且结果非常有趣。在本文中，我将展示如何在Android手机上运行LLaMA和Gemma大型语言模型，我们将看到它是如何工作的。像我所有的测试一样，所有的模型都将在本地运行，不需要使用云API或支付。

让我们开始吧！

## Termux

我们测试的第一个组件是[Termux](https://wiki.termux.com/wiki/Main_Page)，这是一个作为Android应用程序制作的完整Linux终端。它是免费的，并且不需要root权限；所有的Linux组件都仅在Termux文件夹中运行。Termux可以从[Google Play](https://play.google.com/store/apps/details?id=com.termux)下载，但在撰写本文时，那个版本已经比较旧，并且Termux中的“pkg update”命令不再工作了。一个更新版本可以在[F-Droid](https://f-droid.org/en/packages/com.termux/)网站上找到；它运行良好，我没有遇到任何问题。

当Termux安装在手机上时，我们可以运行它并看到一个标准的Linux命令行界面：
