# 忘记统计测试：A/B测试完全是关于模拟的

> 原文：[https://towardsdatascience.com/forget-statistical-tests-a-b-testing-is-all-about-simulations-33efa2241ae2?source=collection_archive---------1-----------------------#2024-07-04](https://towardsdatascience.com/forget-statistical-tests-a-b-testing-is-all-about-simulations-33efa2241ae2?source=collection_archive---------1-----------------------#2024-07-04)

## 模拟如何胜过传统统计方法，因为它们更易理解、更灵活且经济意义更为重要

[](https://medium.com/@mazzanti.sam?source=post_page---byline--33efa2241ae2--------------------------------)[![Samuele Mazzanti](../Images/432477d6418a3f79bf25dec42755d364.png)](https://medium.com/@mazzanti.sam?source=post_page---byline--33efa2241ae2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--33efa2241ae2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--33efa2241ae2--------------------------------) [Samuele Mazzanti](https://medium.com/@mazzanti.sam?source=post_page---byline--33efa2241ae2--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--33efa2241ae2--------------------------------)·阅读11分钟·2024年7月4日

--

![](../Images/9fb601484bd95b091eba4983b1c28784.png)

[作者提供的图片]

公司广泛使用诸如A/B测试之类的对照实验。

然而，许多人对A/B测试感到厌恶，因为其中包含令人生畏的统计术语，包括“置信度”、“功效”、“p值”、“t检验”、“效应大小”等。

在这篇文章中，我将向您展示，您不需要统计学硕士来理解A/B测试 —— 恰恰相反。事实上，模拟可以取代那些100年前必不可少的统计工具。

不仅如此：我还将向您展示，实验的可行性可以通过一种任何公司成员都能理解的东西来衡量，这与“置信度”和“功效”不同：美元。

# 从OEC开始

您的网站有一个结账页面。机器学习团队推出了一个新的推荐模型。他们声称，通过将他们的推荐嵌入到结账页面中，我们可以惊人地增加5%的收入。
