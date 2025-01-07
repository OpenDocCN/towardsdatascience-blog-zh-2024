# QueryGPT — 利用生成式人工智能用自然语言查询你的数据

> 原文：[`towardsdatascience.com/querygpt-63fdfefaa888?source=collection_archive---------6-----------------------#2024-02-02`](https://towardsdatascience.com/querygpt-63fdfefaa888?source=collection_archive---------6-----------------------#2024-02-02)

## 一个由大型语言模型驱动的原型工具，使查询数据库变得像说一个词一样简单。

[](https://zainbaq.medium.com/?source=post_page---byline--63fdfefaa888--------------------------------)![Zain Baquar](https://zainbaq.medium.com/?source=post_page---byline--63fdfefaa888--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--63fdfefaa888--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--63fdfefaa888--------------------------------) [Zain Baquar](https://zainbaq.medium.com/?source=post_page---byline--63fdfefaa888--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--63fdfefaa888--------------------------------) ·10 分钟阅读·2024 年 2 月 2 日

--

![](img/4627d1c8edef02127f653dcb3b29fb75.png)

由[Steve Johnson](https://unsplash.com/@steve_j?utm_source=medium&utm_medium=referral)拍摄，图片来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## **简介**

大型语言模型（LLM），如 ChatGPT，已在科技行业中引起了广泛关注，激励开发者探索各种巧妙的应用。本教程探讨了如何利用 ChatGPT，使用自然语言描述我们想要的数据，来彻底改变我们查询数据库的方式。

我们的重点是实现这个系统的基础版本。虽然它作为概念验证，但也有充分的机会进行重大改进，为令人惊叹的功能铺平道路。

我们希望给 LLM 提供数据库内容的***模式***，并让 LLM 基于该上下文回答问题。模式简单来说就是表格数据的蓝图。它定义了表格的列以及每列中元素的数据类型。对于这个最基本的版本来说，这些信息足以回答大多数用户关于数据的查询。

## **什么是提示工程？**
