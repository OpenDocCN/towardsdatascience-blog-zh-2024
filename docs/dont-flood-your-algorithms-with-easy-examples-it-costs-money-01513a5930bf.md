# 不要让你的算法陷入简单数据的洪流中

> 原文：[https://towardsdatascience.com/dont-flood-your-algorithms-with-easy-examples-it-costs-money-01513a5930bf?source=collection_archive---------8-----------------------#2024-12-05](https://towardsdatascience.com/dont-flood-your-algorithms-with-easy-examples-it-costs-money-01513a5930bf?source=collection_archive---------8-----------------------#2024-12-05)

## 面向经理和工程师的机器学习课程

## 一个常见的错误，导致训练变慢、模型效果差，并且烧钱

[](https://medium.com/@oscarleo?source=post_page---byline--01513a5930bf--------------------------------)[![Oscar Leo](../Images/cc6aa03ee3e83ae70c49770c0614689d.png)](https://medium.com/@oscarleo?source=post_page---byline--01513a5930bf--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--01513a5930bf--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--01513a5930bf--------------------------------) [Oscar Leo](https://medium.com/@oscarleo?source=post_page---byline--01513a5930bf--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--01513a5930bf--------------------------------) ·阅读时间7分钟·2024年12月5日

--

![](../Images/40679b848e978f1d70d8429352ab1e63.png)

图像由作者使用ideogram 2.0创建

欢迎回到新的[面向经理和工程师的机器学习课程](https://medium.com/@oscarleo/list/ml-lessons-for-managers-and-engineers-630e5efff657)，在这里我分享我在管理公司NextML时遇到的错误和误解，从中提炼出的机器学习经验！🔥

今天，我们将讨论一个即使是最有经验的机器学习工程师和数据科学家也常犯的错误。我在各行各业、大小公司，以及各种用例中都见过这个问题。

错误在于在训练过程中给算法提供过多简单的例子，导致学习变慢、泛化能力差，并且对异常值更为敏感。

对大多数企业来说，更为关键的是，机器学习算法的训练缓慢会比必要的速度更快地消耗掉财务资源！

> **注意：** 根据我的经验，经理们做出错误的机器学习和人工智能战略决策，因为他们不理解这些技术。我希望通过提供既有技术理解又有合理推理的课程来改变这种情况。
