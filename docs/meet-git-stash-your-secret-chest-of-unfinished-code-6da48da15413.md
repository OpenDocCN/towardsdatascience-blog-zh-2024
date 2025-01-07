# 认识 Git Stash：你未完成代码的秘密宝箱

> 原文：[https://towardsdatascience.com/meet-git-stash-your-secret-chest-of-unfinished-code-6da48da15413?source=collection_archive---------5-----------------------#2024-10-26](https://towardsdatascience.com/meet-git-stash-your-secret-chest-of-unfinished-code-6da48da15413?source=collection_archive---------5-----------------------#2024-10-26)

![](../Images/f8a6b702d18b279ba798f5fa87716556.png)

图片由[Stefan Steinbauer](https://unsplash.com/@usinglight?utm_source=medium&utm_medium=referral)拍摄，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 精通 Git

## Git的一个强大功能，用于临时保存正在进行的代码

[](https://zluvsand.medium.com/?source=post_page---byline--6da48da15413--------------------------------)[![Zolzaya Luvsandorj](../Images/dd3bb91f8625a6fbe8fd26e56036ad29.png)](https://zluvsand.medium.com/?source=post_page---byline--6da48da15413--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6da48da15413--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--6da48da15413--------------------------------) [Zolzaya Luvsandorj](https://zluvsand.medium.com/?source=post_page---byline--6da48da15413--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6da48da15413--------------------------------) ·阅读时间：6分钟·2024年10月26日

--

假设你在进行代码更改时发现了一个需要立即修复的严重bug。你试图切换分支，但由于当前功能分支有未提交的更改，切换失败。这些更改还没有准备好提交，但又太重要，不能丢弃。在这种需要快速切换上下文的情况下，`git stash`提供了一种优雅的解决方案，可以在不提交的情况下临时安全地存储未完成的代码。本文将探讨如何有效使用`git stash`。

# 📍 1\. 暂存

> 假设我们正在用笔和纸写一封信，但突然需要写另一封更紧急的信并寄出。我们的桌子上只能放一封信。既然已经花了一些时间写完目前的部分，把未完成的信丢掉太浪费了。与其扔掉它，我们可以把它放进一个安全的宝箱里，这样等我们完成更紧急的信后，可以拿出来继续写。这个方法让我们可以迅速开始写更紧急的信并快速寄出，同时又能保存我们写的其他信件内容。在这个类比中，未完成的信件就像是未提交的…
