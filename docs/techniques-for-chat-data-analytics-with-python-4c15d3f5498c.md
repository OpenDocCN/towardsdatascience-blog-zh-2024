# 使用 Python 进行聊天数据分析的技巧

> 原文：[`towardsdatascience.com/techniques-for-chat-data-analytics-with-python-4c15d3f5498c?source=collection_archive---------8-----------------------#2024-10-25`](https://towardsdatascience.com/techniques-for-chat-data-analytics-with-python-4c15d3f5498c?source=collection_archive---------8-----------------------#2024-10-25)

## 第一部分：沟通密度分析

[](https://medium.com/@robinvm?source=post_page---byline--4c15d3f5498c--------------------------------)![Robin von Malottki](https://medium.com/@robinvm?source=post_page---byline--4c15d3f5498c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--4c15d3f5498c--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4c15d3f5498c--------------------------------) [Robin von Malottki](https://medium.com/@robinvm?source=post_page---byline--4c15d3f5498c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--4c15d3f5498c--------------------------------) ·8 分钟阅读·2024 年 10 月 25 日

--

![](img/8608cb9f248cad26d75a263111613caf.png)

图片由[Mikechie Esparagoza](https://www.pexels.com/de-de/foto/foto-des-zitierten-brief-brett-zitats-das-an-der-wand-gehangt-wird-1742370/)提供

图片来源于 Pexels.com

多年来，我们的沟通方式变得越来越数字化。无论是通过即时通讯应用发送简短的文本消息，还是通过电子邮件发送，数字信息交流已经深深嵌入我们的日常生活中。

这导致了数字化数据量的增加。

由于沟通至少涉及两个人，因此它可以揭示参与者及其彼此关系的许多见解。

本文将是一个系列的第一部分，在该系列中，我将展示您可以使用聊天记录做的有趣事情，以及您可以从中获得的个人见解。您可以在我的[GitHub 个人资料](https://github.com/Robinvm96/Chat-Analytics)中找到我为可视化创建的 Python 代码和任何 Tableau 文件。

**目标**

由于这是该系列的第一篇文章，我想从 WhatsApp 聊天的元数据进行高级分析开始。我将即将进行的分析命名为**沟通密度分析**，因为可视化的主要特征来自于发送消息的密度。
