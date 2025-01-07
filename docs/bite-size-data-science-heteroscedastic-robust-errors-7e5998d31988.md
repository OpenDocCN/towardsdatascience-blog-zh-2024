# 一口大小的数据科学：异方差性稳健误差

> 原文：[https://towardsdatascience.com/bite-size-data-science-heteroscedastic-robust-errors-7e5998d31988?source=collection_archive---------6-----------------------#2024-05-29](https://towardsdatascience.com/bite-size-data-science-heteroscedastic-robust-errors-7e5998d31988?source=collection_archive---------6-----------------------#2024-05-29)

## 如何调整标准误差以适应异方差性以及其原理

[](https://medium.com/@jarom.hulet?source=post_page---byline--7e5998d31988--------------------------------)[![Jarom Hulet](../Images/0fdeb1a2df90cccdd8f2f4b84d5e54eb.png)](https://medium.com/@jarom.hulet?source=post_page---byline--7e5998d31988--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--7e5998d31988--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--7e5998d31988--------------------------------) [Jarom Hulet](https://medium.com/@jarom.hulet?source=post_page---byline--7e5998d31988--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--7e5998d31988--------------------------------) ·阅读时长6分钟·2024年5月29日

--

![](../Images/d45b1d7dcd374da78f606854622eb845.png)

基于同方差性假设可以进行的代数操作——图片由作者提供

本文的“一口大小”格式旨在提供关于单一小范围话题的简明、集中的见解。阅读完本文后，你将理解：(1) 为什么同方差性误差是线性回归中有效标准误差的前提，以及 (2) 如何计算异方差性稳健误差，及其为何去除了同方差性假设的需求。

下面是本文的内容：

1.  同方差/异方差误差的快速概述

1.  解释为什么在进行线性回归时需要同方差性假设——以友好的方式推导出来 😃

1.  如何修改标准误差公式以去除同方差性假设

## **异方差性与同方差性误差**

异方差性与同方差性误差是一个被广泛讨论的话题；如果你对此有较好的理解，可以跳过下一部分！这里我会简要概述一下——如果你想了解更多，谷歌是你的好朋友！

参加过统计学基础课程的人都知道，线性回归的一个关键假设是……
