# 最大似然估计介绍

> 原文：[`towardsdatascience.com/introduction-to-maximum-likelihood-estimates-7e37f83c6757?source=collection_archive---------4-----------------------#2024-09-18`](https://towardsdatascience.com/introduction-to-maximum-likelihood-estimates-7e37f83c6757?source=collection_archive---------4-----------------------#2024-09-18)

## 通过其在下一个单词预测中的应用来了解最大似然估计

[](https://medium.com/@rishabh_misra_?source=post_page---byline--7e37f83c6757--------------------------------)![Rishabh Misra](https://medium.com/@rishabh_misra_?source=post_page---byline--7e37f83c6757--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7e37f83c6757--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7e37f83c6757--------------------------------) [Rishabh Misra](https://medium.com/@rishabh_misra_?source=post_page---byline--7e37f83c6757--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7e37f83c6757--------------------------------) ·阅读时间 9 分钟·2024 年 9 月 18 日

--

![](img/e57b1b0c6f06dfc383ae7b277f354474.png)

图片由[freestocks](https://unsplash.com/@freestocks?utm_source=medium&utm_medium=referral)提供，来源：[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 介绍

最大似然估计（MLE）是一种基本方法，能够使任何机器学习模型从可用数据中学习到独特的模式。在这篇博客中，我们将通过将其应用于下一个单词预测问题来学习最大似然估计的概念，以使解释更加直观。

> 注意：本文将使用一些来自概率论和机器学习理论的概念。我会尽力将内容保持尽可能通用，并在引入这些概念时提供相关背景阅读的链接。

# 问题设定

一个优秀键盘应用的常见特性之一是预测即将输入的单词。随着用户使用应用的增加，这些预测会越来越准确，从而节省用户的输入时间。文本预测的另一个应用场景是在搜索引擎中。预测性搜索可以节省输入时间并引导访问者到结果，而不是让他们输入略有偏差的搜索内容，进而无法返回大量的结果。作为这些应用的用户，我相信你曾经想过：“这到底是如何工作的…
