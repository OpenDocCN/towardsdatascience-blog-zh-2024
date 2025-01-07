# 如何设计批处理

> 原文：[https://towardsdatascience.com/how-to-design-a-batch-processing-fc6cd32fd879?source=collection_archive---------4-----------------------#2024-01-10](https://towardsdatascience.com/how-to-design-a-batch-processing-fc6cd32fd879?source=collection_archive---------4-----------------------#2024-01-10)

## 从业务和技术角度理解批处理

[](https://medium.com/@xiaoxugao?source=post_page---byline--fc6cd32fd879--------------------------------)[![Xiaoxu Gao](../Images/8712a7e5f3bad0d2abd7e04792fad66f.png)](https://medium.com/@xiaoxugao?source=post_page---byline--fc6cd32fd879--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fc6cd32fd879--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--fc6cd32fd879--------------------------------) [Xiaoxu Gao](https://medium.com/@xiaoxugao?source=post_page---byline--fc6cd32fd879--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fc6cd32fd879--------------------------------) ·阅读时间10分钟·2024年1月10日

--

![](../Images/e5184d61c5c85edd4ad127b7ebdfa621.png)

摄影：由[Dannie Sorum](https://unsplash.com/@danjoso)提供，来自[Unsplash](https://unsplash.com/)

我们生活在一个每个人类互动都会成为系统中的事件的世界，不管是在线或线下购买衣服、浏览社交媒体，还是打车使用Uber。这些事件都在某种程度上被处理。某些事件需要快速响应，因此它们会立即处理。例如，当完成一次Uber打车后，你会在几秒钟内收到收据。输入和输出通常是1对1的。

![](../Images/49a5edcfc856cde05a71df18692c6cbc.png)

2种不同的数据处理模式

另外一些事件在后台集体处理时能创造更大的价值。举个例子，生成月度报告时，你需要将本月的所有交易合并在一起。输入和输出通常是多对1。这也叫做批处理。

作为数据从业者，我们每天都在处理批次数据。它是一个*传统*但依然非常强大的数据处理方法，每个数据人都应该了解。由于它是如此基础的领域，值得深入探讨。在本文中，我将从批处理的应用场景开始——即企业如何从中受益，接着讲述它的…
