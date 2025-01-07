# 成功的每个机器学习工程师必须知道的事情

> 原文：[https://towardsdatascience.com/what-every-aspiring-machine-learning-engineer-must-know-to-succeed-b49722073048?source=collection_archive---------2-----------------------#2024-12-22](https://towardsdatascience.com/what-every-aspiring-machine-learning-engineer-must-know-to-succeed-b49722073048?source=collection_archive---------2-----------------------#2024-12-22)

## *避免机器学习生产环境中关键错误的指南*

[](https://ds-claudia.medium.com/?source=post_page---byline--b49722073048--------------------------------)[![Claudia Ng](../Images/81d7927943f38f6303690cfd5676c8fd.png)](https://ds-claudia.medium.com/?source=post_page---byline--b49722073048--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--b49722073048--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--b49722073048--------------------------------) [Claudia Ng](https://ds-claudia.medium.com/?source=post_page---byline--b49722073048--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--b49722073048--------------------------------) ·阅读时间 5 分钟·2024年12月22日

--

![](../Images/83cd8ae3a5fe88ddfde84ccb629d9fdb.png)

图片来自 [GrumpyBeere](https://pixabay.com/users/grumpybeere-22072131/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=8829735) 由 [Pixabay](https://pixabay.com//?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=8829735) 提供

我永远不会忘记第一次收到PagerDuty警报，提醒我生产环境中的模型评分没有正确返回。

我开始陷入慌乱——我刚刚进行了一次部署，我的脑海中充满了各种问题：

+   我的代码是否引发了一个bug？

+   错误是否导致了下游的服务中断？

+   代码的哪一部分可能会抛出错误？

调试实时系统非常紧张，我学到了一个关键教训：*编写适用于生产环境的代码与在Jupyter Notebook中编写能运行的代码完全不同*。

2020年，我从数据分析师转型为机器学习工程师（MLE）。尽管我已经熟练掌握了SQL和Python，但与生产系统的工作让我迫使自己提升技能。

作为一名分析师，我最关心的是我的代码是否能够运行并生成正确的输出。然而，这种心态已经不再适用于成为一名机器学习工程师（MLE）。

作为一名MLE，我很快意识到，我必须专注于编写高效、简洁且易于维护的代码，这些代码能够在共享的代码库中正常工作。
