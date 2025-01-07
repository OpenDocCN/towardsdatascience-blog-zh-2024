# 为什么Python消耗如此多的内存？

> 原文：[https://towardsdatascience.com/why-does-an-integer-need-28-bytes-in-python-e8adf91f821f?source=collection_archive---------3-----------------------#2024-05-20](https://towardsdatascience.com/why-does-an-integer-need-28-bytes-in-python-e8adf91f821f?source=collection_archive---------3-----------------------#2024-05-20)

![](../Images/d8a9d2f3fa9e2f61aa1ecc7d5b47f128.png)

图片来自Canva.com

## Python中整数对象的内存消耗分解

[](https://christophertao.medium.com/?source=post_page---byline--e8adf91f821f--------------------------------)[![Christopher Tao](../Images/bea1e3c81cc62eb28bdba9275d6b326f.png)](https://christophertao.medium.com/?source=post_page---byline--e8adf91f821f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e8adf91f821f--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--e8adf91f821f--------------------------------) [Christopher Tao](https://christophertao.medium.com/?source=post_page---byline--e8adf91f821f--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e8adf91f821f--------------------------------) ·阅读时长7分钟·2024年5月20日

--

我们都知道，Python因其灵活性和动态特性而闻名。然而，你是否思考过它为什么能够实现这些特性，是否有任何代价？当然，编程语言的具体机制太复杂，无法通过一篇文章来解释清楚。今天我想展示的是一个关于Python内存消耗的非常简单的示例。

在本文中，我将向你展示如何衡量一个变量占用多少内存，以及Python中会导致相对较高内存消耗的一些开销。如果你不明白为什么Python需要至少28个字节来存储一个简单的整数，请不要错过接下来的内容。

# 1\. 如何衡量一个变量的内存使用情况？

![](../Images/7acbb9e908ceb63358df6d66be9df1c7.png)

图片来自Canva.com

在我们开始讲解示例之前，我们需要一个用于衡量内存使用情况的解决方案。否则，我的示例将缺乏说服力。一旦我们掌握了方法，我们也可以在日常工作中用它来衡量自己的实现。
