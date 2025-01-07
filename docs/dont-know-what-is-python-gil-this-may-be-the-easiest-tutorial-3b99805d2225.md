# 可能是最简单的 Python 线程、进程和 GIL 教程

> 原文：[https://towardsdatascience.com/dont-know-what-is-python-gil-this-may-be-the-easiest-tutorial-3b99805d2225?source=collection_archive---------4-----------------------#2024-02-05](https://towardsdatascience.com/dont-know-what-is-python-gil-this-may-be-the-easiest-tutorial-3b99805d2225?source=collection_archive---------4-----------------------#2024-02-05)

![](../Images/da1186ff350c7481a5485a4ad8ed6cda.png)

图片来源：[Regina](https://pixabay.com/users/r391n4-3182451/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2171040) 来自 [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=2171040)

## 通过图示和代码进行说明，减少枯燥的概念

[](https://christophertao.medium.com/?source=post_page---byline--3b99805d2225--------------------------------)[![Christopher Tao](../Images/bea1e3c81cc62eb28bdba9275d6b326f.png)](https://christophertao.medium.com/?source=post_page---byline--3b99805d2225--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3b99805d2225--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3b99805d2225--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--3b99805d2225--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3b99805d2225--------------------------------) ·7 分钟阅读·2024年2月5日

--

如果你是 Python 学习者，不要跑开，因为这篇文章旨在用最简单的方式解释给你什么是 GIL。当然，必须先从解释线程和进程是什么开始。别担心，我会尽力让它变得简单，尽管这可能会牺牲一些定义的准确性。

现在我们应该开始了。

# 1\. Python 中的多线程

![](../Images/e512eda1afcdf85d17718e660377fd98.png)

图片来源：[Steen Jepsen](https://pixabay.com/users/steenjepsen-1490089/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1232723) 来自 [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1232723)

## 一些概念

多线程是最常见的编程技术之一，它也存在于 Python 中。

它允许我们同时运行多个操作。通常，多线程可以提高 CPU 的使用效率。此外，大多数 I/O 任务也能从并发运行的线程中受益。

请不要混淆“**进程**”和“**线程**”这两个概念。进程会分配一定的内存，并且在操作系统中与其他进程完全隔离。因此，我们的操作系统中的一个程序崩溃了…
