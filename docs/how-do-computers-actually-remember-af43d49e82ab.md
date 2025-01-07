# 计算机是如何记住东西的？

> 原文：[https://towardsdatascience.com/how-do-computers-actually-remember-af43d49e82ab?source=collection_archive---------8-----------------------#2024-06-20](https://towardsdatascience.com/how-do-computers-actually-remember-af43d49e82ab?source=collection_archive---------8-----------------------#2024-06-20)

## 初学者数据科学家与计算机硬件的介绍

[](https://medium.com/@cadenhamrick?source=post_page---byline--af43d49e82ab--------------------------------)[![W. Caden Hamrick](../Images/1674ac4b1c844f3aff37b8e3083764da.png)](https://medium.com/@cadenhamrick?source=post_page---byline--af43d49e82ab--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--af43d49e82ab--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--af43d49e82ab--------------------------------) [W. Caden Hamrick](https://medium.com/@cadenhamrick?source=post_page---byline--af43d49e82ab--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--af43d49e82ab--------------------------------) ·12分钟阅读·2024年6月20日

--

# 介绍

你是否曾经想过计算机究竟是如何*记住*东西的？当然，你可能听说过它使用RAM作为“短期”记忆，而你的HDD或SSD则是“长期”记忆。但像这些设备是如何真正保存信息的呢？这就是我们在本文中要回答的问题。

![](../Images/edf991442fb3a62f14cf54a5729aefe2.png)

图片由[Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

作为数据科学家，我们通常使用的是高级工具。Python很棒，但它为了简化工作，隐藏了许多让计算机运作的细节。然而，计算机是我们工作中的工具——难道我们不应该理解它们是如何工作的吗？本文是我系列文章《初学者数据科学家与计算机硬件的介绍》的第三篇。如果你错过了前两篇文章，不用担心——你不需要它们也能继续阅读，但它们会帮助你更深入地理解（如果你想阅读其他文章，可以在本文底部找到相关链接）。

本系列的目的是让新的（或有经验的）数据科学家了解他们每天使用的工具是如何运作的，而不需要具备物理学、电气工程学或低级计算机科学的知识。如果你对计算机如何运作感到好奇……
