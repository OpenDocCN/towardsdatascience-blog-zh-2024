# 使用Python进行聊天数据分析的技术

> 原文：[https://towardsdatascience.com/techniques-for-chat-data-analytics-with-python-cfdbea358123?source=collection_archive---------5-----------------------#2024-11-15](https://towardsdatascience.com/techniques-for-chat-data-analytics-with-python-cfdbea358123?source=collection_archive---------5-----------------------#2024-11-15)

## 第二部分：使用BERTopic进行主题提取

[](https://medium.com/@robinvm?source=post_page---byline--cfdbea358123--------------------------------)[![Robin von Malottki](../Images/f5724e8eecb4925706a83f353caa303a.png)](https://medium.com/@robinvm?source=post_page---byline--cfdbea358123--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cfdbea358123--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cfdbea358123--------------------------------) [Robin von Malottki](https://medium.com/@robinvm?source=post_page---byline--cfdbea358123--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cfdbea358123--------------------------------) ·阅读时长10分钟·2024年11月15日

--

![](../Images/2f3f8482931a2bb30ec6a1f04901465b.png)

图片来源：[Mikechie Esparagoza](https://www.pexels.com/de-de/foto/foto-des-zitierten-brief-brett-zitats-das-an-der-wand-gehangt-wird-1742370/)

图片来源：Pexels.com

在本系列的第一部分，我向大家介绍了我人工创建的朋友约翰，他非常友好地提供了他与五个最亲近的人的聊天记录。我们仅使用了元数据，比如谁在什么时间发送了消息，以可视化约翰何时遇到他的女朋友，何时与最好的朋友之一发生争执，以及他应该更常给哪些家人写信。如果你还没有阅读本系列的第一部分，可以在[这里](https://medium.com/towards-data-science/techniques-for-chat-data-analytics-with-python-4c15d3f5498c)找到它。

我们还没有涉及的内容，但接下来会深入探讨的是对实际消息的分析。因此，我们将使用约翰和玛丽亚之间的聊天记录来识别他们讨论的主题。当然，我们不会一条一条地查看这些消息并进行分类——不，我们将使用Python库BERTopic来提取这些聊天围绕的主题。

**什么是BERTopic？**

BERTopic是一种由Maarten Grootendorst提出的主题建模技术，利用基于变换器的嵌入，特别是BERT嵌入，从大量文档中生成连贯且易于解释的主题。它旨在克服传统主题建模方法如LDA（潜在狄利克雷分配）的局限性，因为传统方法通常难以处理简短的文本…
