# 使用Python和GitHub Actions自动化数据管道

> 原文：[https://towardsdatascience.com/automating-data-pipelines-with-python-github-actions-c19e2ef9ca90?source=collection_archive---------2-----------------------#2024-05-30](https://towardsdatascience.com/automating-data-pipelines-with-python-github-actions-c19e2ef9ca90?source=collection_archive---------2-----------------------#2024-05-30)

## 一种简单（且免费的）运行数据工作流的方法

[](https://shawhin.medium.com/?source=post_page---byline--c19e2ef9ca90--------------------------------)[![Shaw Talebi](../Images/1449cc7c08890e2078f9e5d07897e3df.png)](https://shawhin.medium.com/?source=post_page---byline--c19e2ef9ca90--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c19e2ef9ca90--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c19e2ef9ca90--------------------------------) [Shaw Talebi](https://shawhin.medium.com/?source=post_page---byline--c19e2ef9ca90--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c19e2ef9ca90--------------------------------) ·阅读时间9分钟·2024年5月30日

--

这是关于全栈数据科学（FSDS）系列中的第4篇文章，[更大的系列](https://shawhin.medium.com/list/full-stack-data-science-f0910c75d006)。在[上一篇文章](/how-to-build-data-pipelines-for-machine-learning-b97bbef050a5)中，我分享了一个具体的示例，展示了如何为机器学习项目构建数据管道。然而，这个示例的一个限制是数据管道必须手动运行。虽然这对于某些应用来说可能没问题，但更多时候，自动化比让人手动执行这个过程要更好。在这篇文章中，我将介绍一种使用Python和GitHub Actions自动化这一过程的简单方法。

![](../Images/fbe474e2de536c41b975274c1bba3f97.png)

图片来源：[Chen Mizrach](https://unsplash.com/@chenhanozel?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

几年前，Andrej Karpathy 发表了一次演讲，描述了“Operation Vacation”[1]。这就是特斯拉全自动驾驶工程团队所称的目标——完全自动化自驾模型的改进。

尽管这个目标有些玩笑性质，但它展示了我在大多数数据科学家和工程师中看到的一个愿望：**希望构建一个可以自主运行的系统**（这样他们就可以去度假）。

在这里，我将讨论如何自动化任何机器学习系统中的一个关键元素——数据管道。
