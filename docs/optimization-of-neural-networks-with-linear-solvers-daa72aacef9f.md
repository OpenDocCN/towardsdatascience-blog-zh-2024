# 使用线性求解器优化神经网络

> 原文：[https://towardsdatascience.com/optimization-of-neural-networks-with-linear-solvers-daa72aacef9f?source=collection_archive---------9-----------------------#2024-02-16](https://towardsdatascience.com/optimization-of-neural-networks-with-linear-solvers-daa72aacef9f?source=collection_archive---------9-----------------------#2024-02-16)

## **如何使用线性求解器优化多维非线性神经网络。**

[](https://medium.com/@mit.forster?source=post_page---byline--daa72aacef9f--------------------------------)[![Tim Forster](../Images/0159a256b7fa28c674a1d5e1489e0df6.png)](https://medium.com/@mit.forster?source=post_page---byline--daa72aacef9f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--daa72aacef9f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--daa72aacef9f--------------------------------) [Tim Forster](https://medium.com/@mit.forster?source=post_page---byline--daa72aacef9f--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--daa72aacef9f--------------------------------) ·19分钟阅读·2024年2月16日

--

![](../Images/1de53e567b4626496fd6ab2518a96147.png)

图片来自[Sam Moghadam Khamseh](https://unsplash.com/@sammoghadamkhamseh?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)上的分享

> 注意：如果你没有Medium订阅，可以[免费阅读文章](https://medium.com/@mit.forster?source=post_page---byline--daa72aacef9f--------------------------------)（如果你有订阅，请继续阅读，谢谢！🥰）

最近，我遇到了一个问题，要求我创建一个能够接受多个输入特征并预测连续输出的模型。

然后，我需要从该模型中获得最佳输出，对我来说是最低的数值。换句话说，我需要解决一个优化问题。

问题是（直到那个阶段我才意识到）我所在的环境不允许我使用非线性方法或复杂的框架——所以不能使用神经网络，不能使用非线性求解器，什么都不行……

但是，我创建的模型运行得很好（考虑到我用来训练它的数据点数量很少），而且我不想删除所有代码，从头开始使用线性模型。

所以，在喝了一杯咖啡之后☕，我决定使用这个已经训练好的非线性模型，生成一些小的线性模型。然后，我可以使用线性求解器来解决优化问题。
