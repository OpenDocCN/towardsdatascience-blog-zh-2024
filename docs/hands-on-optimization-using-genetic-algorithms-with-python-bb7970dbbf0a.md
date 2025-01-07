# 《使用遗传算法进行动手优化，Python 实现》

> 原文：[`towardsdatascience.com/hands-on-optimization-using-genetic-algorithms-with-python-bb7970dbbf0a?source=collection_archive---------2-----------------------#2024-09-29`](https://towardsdatascience.com/hands-on-optimization-using-genetic-algorithms-with-python-bb7970dbbf0a?source=collection_archive---------2-----------------------#2024-09-29)

## 这是关于遗传算法的完整指南，介绍了它们是什么以及如何使用它们

[](https://piero-paialunga.medium.com/?source=post_page---byline--bb7970dbbf0a--------------------------------)![Piero Paialunga](https://piero-paialunga.medium.com/?source=post_page---byline--bb7970dbbf0a--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bb7970dbbf0a--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bb7970dbbf0a--------------------------------) [Piero Paialunga](https://piero-paialunga.medium.com/?source=post_page---byline--bb7970dbbf0a--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bb7970dbbf0a--------------------------------) ·阅读时间 13 分钟·2024 年 9 月 29 日

--

![](img/96ebe4093d348d361bc43324ec1860b5.png)

图片来源：[digitale.de](https://unsplash.com/@digital_e?utm_source=medium&utm_medium=referral) 于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

你听说过这种销售策略吗？

> “你是否厌倦了在 X 上浪费时间？为什么不试试 Y 呢！”

我敢肯定你听说过。例如：***“不要花费几个小时编写自己的代码，使用我们的软件！”*** 或者“***别再浪费几个小时在广告上，改用 AI 吧！***”（我的 YouTube 推荐算法**非常喜欢**这个，可能是我的错，因为我总是谈论 AI）。

这种销售策略基于这样一个事实：你在想要解决的**问题**上花费的**时间**是可以**优化**的。

***“从极客角度来说”***，当你看到这种销售策略时，通常有两个选择：**探索**或**开发**。我是什么意思呢？稍后我会解释。现在，记住这个销售策略，把它放在脑后，稍后我们会再回到它。

假设你有一个工具，一个可以调整的旋钮。通过移动这个旋钮，你可以得到一个**等待时间**。想象一下，你在星巴克排队等咖啡，旋钮有两个状态（0 和 1）：0 可能意味着“待在原来的队伍里”，而 1 可能意味着“换到另一个队伍”。哪一个意味着“等待最少的时间”...
