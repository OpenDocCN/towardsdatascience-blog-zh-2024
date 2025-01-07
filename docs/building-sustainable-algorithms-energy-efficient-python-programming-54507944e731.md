# 构建可持续算法：节能高效的 Python 编程

> 原文：[https://towardsdatascience.com/building-sustainable-algorithms-energy-efficient-python-programming-54507944e731?source=collection_archive---------2-----------------------#2024-11-23](https://towardsdatascience.com/building-sustainable-algorithms-energy-efficient-python-programming-54507944e731?source=collection_archive---------2-----------------------#2024-11-23)

## 降低 Python 算法计算成本的 6 种技巧

[](https://arijoury.medium.com/?source=post_page---byline--54507944e731--------------------------------)[![Ari Joury, PhD](../Images/5b9e49279fb3f26373b393f29a4daaf7.png)](https://arijoury.medium.com/?source=post_page---byline--54507944e731--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--54507944e731--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--54507944e731--------------------------------) [Ari Joury, PhD](https://arijoury.medium.com/?source=post_page---byline--54507944e731--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--54507944e731--------------------------------) ·9 分钟阅读·2024年11月23日

--

![](../Images/40f83342a9ea2ab631d4d1138fc843e3.png)

你可以通过使用这些技巧来提高 Python 的性能。图像由 Leonardo AI 生成

一名初级软件开发人员若因代码能够运行而感到高兴，应该得到宽容。如果你是这样的人，我不做评判。

然而，如果你准备好提升你的 Python 软件开发技能，你的代码不应该只是运行并通过一些测试。它还应该考虑到可用的计算资源——以及电费——来编写。

每一个低效的循环、选择不当的数据结构或冗余的计算都会消耗更多的电力。与 C 语言不同，例如，在 C 中，你必须为每个新创建的变量保留磁盘空间，而 Python 会根据需要消耗资源。这使得 Python 对初学者极为友好，但如果使用不当，也会非常耗能。

粗心的算法不仅对代码性能不好，也对地球有害。像[微软这样的软件公司](https://www.geekwire.com/2024/microsofts-carbon-footprint-keeps-growing-as-ai-drives-data-center-expansions/)因为为 AI 和其他任务消耗大量能源，正努力保持碳排放量低。与此同时，可持续性问题日益受到关注。因此，注重可持续性的程序员正成为许多公司宝贵的资源。
