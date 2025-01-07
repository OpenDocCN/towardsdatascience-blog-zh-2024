# 深入探讨赔率比

> 原文：[https://towardsdatascience.com/a-deep-dive-into-odds-ratio-7c327b2b1ac7?source=collection_archive---------5-----------------------#2024-09-24](https://towardsdatascience.com/a-deep-dive-into-odds-ratio-7c327b2b1ac7?source=collection_archive---------5-----------------------#2024-09-24)

## 赔率比系列深度解析第一部分

## 了解、计算、可视化和解释赔率比及其置信区间，并通过Python中的实际示例进行演示。

[](https://miqbalrp.medium.com/?source=post_page---byline--7c327b2b1ac7--------------------------------)[![Iqbal Rahmadhan](../Images/778d9cb795065223ec8fa53001de1815.png)](https://miqbalrp.medium.com/?source=post_page---byline--7c327b2b1ac7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7c327b2b1ac7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7c327b2b1ac7--------------------------------) [Iqbal Rahmadhan](https://miqbalrp.medium.com/?source=post_page---byline--7c327b2b1ac7--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7c327b2b1ac7--------------------------------) ·阅读时间：17分钟·2024年9月24日

--

![](../Images/fca779a9d6b0241962e2d17460b1aba9.png)

图片来源：[Christian Stahl](https://unsplash.com/@woodpecker65?utm_source=medium&utm_medium=referral)在[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

你可能听过类似这样的一句话：“吸烟者患肺癌的可能性是非吸烟者的五倍。”尽管这个例子并非来自真实的研究，仅用于说明，但它为我们讨论赔率比提供了一个很好的出发点。

> **还不是Medium会员？继续使用此**[**免费版**](https://medium.com/towards-data-science/a-deep-dive-into-odds-ratio-7c327b2b1ac7?sk=6fcfc9d93cf0b407a85e6ed7265f4a4f)**！**

赔率比是一个相对简单的概念，实际上也很容易解释。在本文中，我们将讨论赔率和赔率比，提供清晰的逐步计算解释，并展示如何使用Python中的SciPy库进行快速计算。最后，我们将探讨如何有效地传达和解释赔率比。

# 什么是赔率比？

我使用上述例子是因为赔率比在临床研究中有着重要应用，用于量化暴露与结果之间的关系。顾名思义，**赔率比比较了事件（或疾病）发生的赔率**……
