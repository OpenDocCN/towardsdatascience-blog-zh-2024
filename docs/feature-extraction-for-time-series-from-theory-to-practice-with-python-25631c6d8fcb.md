# 时间序列特征提取，从理论到实践，使用Python

> 原文：[https://towardsdatascience.com/feature-extraction-for-time-series-from-theory-to-practice-with-python-25631c6d8fcb?source=collection_archive---------0-----------------------#2024-08-24](https://towardsdatascience.com/feature-extraction-for-time-series-from-theory-to-practice-with-python-25631c6d8fcb?source=collection_archive---------0-----------------------#2024-08-24)

## 这里是你需要知道的，关于时间序列分析中特征提取的所有内容

[](https://piero-paialunga.medium.com/?source=post_page---byline--25631c6d8fcb--------------------------------)[![Piero Paialunga](../Images/de2185596a49484698733e85114dd1ff.png)](https://piero-paialunga.medium.com/?source=post_page---byline--25631c6d8fcb--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--25631c6d8fcb--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--25631c6d8fcb--------------------------------) [Piero Paialunga](https://piero-paialunga.medium.com/?source=post_page---byline--25631c6d8fcb--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--25631c6d8fcb--------------------------------) ·阅读时间11分钟·2024年8月24日

--

![](../Images/c3cad717a51281510acc5b459686a0a3.png)

图片由[Harman Sandhu](https://unsplash.com/@harryxsandhu?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/photos/white-time-is-precious-signage-building-at-daytime-FpYoDqGGI4A?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

**时间序列**是一个特殊的存在。 

当我开始我的**机器学习**职业生涯时，我是因为我热爱物理（这是个奇怪的理由来开始做机器学习），从物理学中我明白了自己也非常喜欢编程和数据科学。我并不在乎数据的**类型**。我所想要的只是坐在电脑前，每天写10,000行代码。

事实是，即使你不在乎（我仍然真的不在乎），你的职业生涯会把你引导到某些数据类型，而不是其他类型。

如果你在**SpaceX**工作，你可能不会做太多自然语言处理（NLP），但你会做很多**信号**处理。如果你在**Netflix**工作，你可能会最终从事大量的**NLP**和**推荐****系统**。如果你在**Tesla**工作，你肯定会成为一名**计算机视觉**专家，处理图像。

当我开始作为物理学家，并继续攻读工程学博士学位时，我立即被抛入了**信号的世界**。

这只是**工程**的自然世界：每当你有一个设置并从中提取信息时，最终你是在处理一个信号。别误会我的意思……
