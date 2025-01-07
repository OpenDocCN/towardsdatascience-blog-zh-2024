# 机器学习项目的 Git 工作流：我在项目中使用的 Git 工作流

> 原文：[https://towardsdatascience.com/git-workflow-for-machine-learning-projects-the-git-workflow-i-use-in-my-projects-c9f108a00e11?source=collection_archive---------7-----------------------#2024-02-16](https://towardsdatascience.com/git-workflow-for-machine-learning-projects-the-git-workflow-i-use-in-my-projects-c9f108a00e11?source=collection_archive---------7-----------------------#2024-02-16)

## 机器学习和数据科学项目的简单 Git 工作流

[](https://medium.com/@Chim-SO?source=post_page---byline--c9f108a00e11--------------------------------)[![Chayma Zatout](../Images/341c45f53ddf73dc0851d547cc7cb55a.png)](https://medium.com/@Chim-SO?source=post_page---byline--c9f108a00e11--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c9f108a00e11--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c9f108a00e11--------------------------------) [Chayma Zatout](https://medium.com/@Chim-SO?source=post_page---byline--c9f108a00e11--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c9f108a00e11--------------------------------) ·阅读时间 6 分钟·2024年2月16日

--

![](../Images/d572ab1451cf8d4b2a61db13480349bc.png)

图片来源：[Mika Baumeister](https://unsplash.com/@kommumikation?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

在你的项目中采用 Git 工作流可以简化项目管理并提高一致性。有几种 Git 工作流是专门为满足 Git 用户需求而设计的：有些工作流简单直接，有些则更加复杂，适用于大型项目。在本文中，我将分享我在机器学习和数据科学项目中使用的 Git 工作流。我的工作流介于简单与复杂之间——既不太简单，也不太复杂。事不宜迟，让我们开始吧！

还不是 Medium 的会员？没关系！继续阅读，点击这个 [*朋友链接*](/git-workflow-for-machine-learning-projects-the-git-workflow-i-use-in-my-projects-c9f108a00e11?sk=d630ce43c80446df55533521c87c638f)。

# 引言

Git 工作流被定义为一组约定和实践，旨在标准化项目版本控制的管理，从而提高一致性并促进协作。在之前的教程中，我详细介绍了我认为最重要的三种工作流：即 **功能分支** 工作流、**分叉工作流** 和 **Gitflow 工作流**。

在 **功能分支** 工作流中，为每个功能开发、修复 bug 和其他项目任务创建专门的分支。在 **分叉** 工作流中…
