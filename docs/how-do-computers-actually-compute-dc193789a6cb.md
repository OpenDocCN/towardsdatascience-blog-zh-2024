# 计算机究竟是如何进行计算的？

> 原文：[https://towardsdatascience.com/how-do-computers-actually-compute-dc193789a6cb?source=collection_archive---------5-----------------------#2024-06-10](https://towardsdatascience.com/how-do-computers-actually-compute-dc193789a6cb?source=collection_archive---------5-----------------------#2024-06-10)

## 数据科学新手的计算机硬件入门

[](https://medium.com/@cadenhamrick?source=post_page---byline--dc193789a6cb--------------------------------)[![W. Caden Hamrick](../Images/1674ac4b1c844f3aff37b8e3083764da.png)](https://medium.com/@cadenhamrick?source=post_page---byline--dc193789a6cb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--dc193789a6cb--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--dc193789a6cb--------------------------------) [W. Caden Hamrick](https://medium.com/@cadenhamrick?source=post_page---byline--dc193789a6cb--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dc193789a6cb--------------------------------) ·9分钟阅读·2024年6月10日

--

# 入门知识

你常常听到计算机被称为“数字计算机”。它们以远远超过人类能力的速度处理数据，并能每秒做出成千上万次逻辑决策。但它们是如何实现这一点的呢？这些非常昂贵的“石块盒子”有什么特别之处，竟能在某些方面超越其创造者的思维？这就是我们在本文中要解答的问题。

![](../Images/251a483957f2a35db5d364b2c8b9b267.png)

图片来源：[Clément Hélardot](https://unsplash.com/@clemhlrdt?utm_source=medium&utm_medium=referral) 于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

这是我新系列的第二篇文章，*《数据科学新手的计算机硬件入门》*。本系列不假设读者具备任何物理学、电气工程或低级计算机科学的背景，旨在为希望加深对工作工具理解的新手（或有经验的）数据科学家提供计算机硬件的入门知识。毕竟，哪个优秀的工匠不想熟悉自己的工具呢？

你不需要先阅读系列中的前一篇文章就能跟得上本篇内容（不过，如果你想的话，可以在这里阅读：[为什么计算机使用二进制？](https://medium.com/towards-data-science/why-do-computers-even-use-binary-27e6d99f691d)）。如果你对计算机的工作原理感兴趣，但没有太多相关背景知识，那么这个系列将是一个很好的入门选择。
