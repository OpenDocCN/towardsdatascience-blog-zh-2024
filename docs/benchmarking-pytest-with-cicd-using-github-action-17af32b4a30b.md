# 使用GitHub Actions在CICD中进行Pytest基准测试

> 原文：[https://towardsdatascience.com/benchmarking-pytest-with-cicd-using-github-action-17af32b4a30b?source=collection_archive---------12-----------------------#2024-03-05](https://towardsdatascience.com/benchmarking-pytest-with-cicd-using-github-action-17af32b4a30b?source=collection_archive---------12-----------------------#2024-03-05)

## 让Pytest基准测试变得自动化、可操作并且直观

[](https://kayjanwong.medium.com/?source=post_page---byline--17af32b4a30b--------------------------------)[![Kay Jan Wong](../Images/28e803eca6327d97b6aa97ee4095d7bd.png)](https://kayjanwong.medium.com/?source=post_page---byline--17af32b4a30b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--17af32b4a30b--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--17af32b4a30b--------------------------------) [Kay Jan Wong](https://kayjanwong.medium.com/?source=post_page---byline--17af32b4a30b--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--17af32b4a30b--------------------------------) ·阅读时间：7分钟·2024年3月5日

--

![](../Images/91d1029fc1a185f52d71c7f6b3f2c6ce.png)

图片由[Lucas Santos](https://unsplash.com/@_staticvoid?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

“你的代码很慢”是一个常见的说法，但要找出代码的哪一部分慢，究竟有多慢，却需要进行大量的反复试验和测试，而且*慢*到底有多慢呢？一旦找到代码的瓶颈，它是否能在输入扩大到100倍或1000倍的情况下良好扩展，并且结果是基于10次迭代的平均值？

> 这就是`pytest-benchmark`派上用场的地方

补充单元测试的概念，单元测试是对代码库中单一单元或小部分进行测试，我们可以在此基础上拓展，利用`pytest-benchmark`轻松地测量代码性能。

本文将介绍如何设置、运行并解释`pytest-benchmark`的基准测试结果。为了在项目中正确实施基准测试，文章的高级部分还将探讨如何**比较不同运行之间的基准测试结果**并在结果未达到某些阈值时拒绝提交，如何**存储并查看历史基准测试结果**，并通过箱型图和折线图呈现这些结果！
