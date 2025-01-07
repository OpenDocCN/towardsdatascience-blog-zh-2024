# 非线性函数的优化通过分段线性化

> 原文：[`towardsdatascience.com/optimization-of-nonlinear-functions-via-piecewise-linearization-f00fe3c4c5fd?source=collection_archive---------10-----------------------#2024-01-10`](https://towardsdatascience.com/optimization-of-nonlinear-functions-via-piecewise-linearization-f00fe3c4c5fd?source=collection_archive---------10-----------------------#2024-01-10)

## 如何使用免费的混合整数线性求解器来解决非线性优化问题。一个逐步的示例。

[](https://medium.com/@mit.forster?source=post_page---byline--f00fe3c4c5fd--------------------------------)![Tim Forster](https://medium.com/@mit.forster?source=post_page---byline--f00fe3c4c5fd--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f00fe3c4c5fd--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f00fe3c4c5fd--------------------------------) [Tim Forster](https://medium.com/@mit.forster?source=post_page---byline--f00fe3c4c5fd--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f00fe3c4c5fd--------------------------------) ·12 分钟阅读·2024 年 1 月 10 日

--

![](img/7004bc90ba25ab2e37a1fb08d78edbd9.png)

图片由 [Jon Tyson](https://unsplash.com/@jontyson?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

> 注意：如果你没有中等订阅权限，你可以在这里免费阅读文章（如果你有订阅，请继续在这里阅读，谢谢！🥰）

本文是试图理解如何将包含非线性项的模型转化为线性模型的结果。通常，人们希望这样做有几个原因：

+   非线性模型可能非常难以求解。

+   线性模型可能更容易理解和解释。

+   线性模型通常求解速度更快。

+   还有许多其他（特定案例的）原因。

如果你所在的大学或公司没有访问强大（混合整数）非线性求解器的权限，比如[BARON](http://link.springer.com/10.1007/BF00138693)、[ANTIGONE](https://link.springer.com/article/10.1007/s10898-014-0166-2)等，并且你遇到了非线性问题，那么一个选择是尝试将模型线性化，以便使用线性求解器。

我知道你现在可能会想，在“仅非线性且没有整数变量”的情况下，你也可以使用像[IPOPT](https://coin-or.github.io/Ipopt/index.html)这样的公开可用求解器。或者如果你想使用其他……
