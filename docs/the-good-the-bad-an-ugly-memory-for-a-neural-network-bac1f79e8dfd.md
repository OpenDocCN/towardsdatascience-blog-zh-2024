# 好的、坏的和丑的：神经网络的记忆

> 原文：[https://towardsdatascience.com/the-good-the-bad-an-ugly-memory-for-a-neural-network-bac1f79e8dfd?source=collection_archive---------6-----------------------#2024-12-17](https://towardsdatascience.com/the-good-the-bad-an-ugly-memory-for-a-neural-network-bac1f79e8dfd?source=collection_archive---------6-----------------------#2024-12-17)

## |人工智能|记忆|神经网络|学习|

## 记忆可能会耍花招；要学得最好，记忆并不总是有效的

[](https://salvatore-raieli.medium.com/?source=post_page---byline--bac1f79e8dfd--------------------------------)[![Salvatore Raieli](../Images/6bb4520e2df40d20283e7283141b5e06.png)](https://salvatore-raieli.medium.com/?source=post_page---byline--bac1f79e8dfd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--bac1f79e8dfd--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--bac1f79e8dfd--------------------------------) [Salvatore Raieli](https://salvatore-raieli.medium.com/?source=post_page---byline--bac1f79e8dfd--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--bac1f79e8dfd--------------------------------) ·12分钟阅读·2024年12月17日

--

![](../Images/b0b6fc0d8316c04da45469a33f2812a6.png)

由作者使用DALL-E生成的图像

> 没有人有足够好的记忆力，能够成为一个成功的骗子。——亚伯拉罕·林肯
> 
> 记忆比墨水更难忘。——安妮塔·卢斯

**记忆不好，泛化好。** 这是人工智能的一个教条。但为什么呢？记忆有什么问题？

直观地说，一个背下整本书的学生，如果考试中的练习题与书中的内容不同，可能仍然会失败。如果背诵并不等于学习，有时适当的记忆也有益。例如，学习复杂的规则来记住一串历史人物的名字是没有意义的。你必须知道如何找到合适的平衡。神经网络中也发生着类似的事情。本文讨论了神经网络与记忆之间复杂的爱/恨关系。

***人工智能正在改变我们的世界，塑造我们的生活和工作方式。理解它如何运作以及它的影响，比以往任何时候都更加重要。*** *如果*…
