# 我正在做 2024 年的圣诞编程挑战 — 第4天

> 原文：[https://towardsdatascience.com/im-doing-the-advent-of-code-2024-in-python-day-4-f0dacf4529a6?source=collection_archive---------5-----------------------#2024-12-24](https://towardsdatascience.com/im-doing-the-advent-of-code-2024-in-python-day-4-f0dacf4529a6?source=collection_archive---------5-----------------------#2024-12-24)

## 让我们看看我们能收集多少颗星星。

[](https://sonery.medium.com/?source=post_page---byline--f0dacf4529a6--------------------------------)[![Soner Yıldırım](../Images/c589572e9d1ee176cd4f5a0008173f1b.png)](https://sonery.medium.com/?source=post_page---byline--f0dacf4529a6--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f0dacf4529a6--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f0dacf4529a6--------------------------------) [Soner Yıldırım](https://sonery.medium.com/?source=post_page---byline--f0dacf4529a6--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f0dacf4529a6--------------------------------) ·6分钟阅读·2024年12月24日

--

![](../Images/1a398e58181b334743b2cec4a15b676c.png)

图片来源：[Nong](https://unsplash.com/@californong?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash) 来自 [Unsplash](https://unsplash.com/photos/white-and-brown-elephant-figurine-3XisDwg6jAE?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

欢迎来到第4天！

+   [第1天](https://medium.com/towards-data-science/im-doing-the-advent-of-code-2024-in-python-day-1-8a9ea6ca6d3f) 介绍了如何获取自己谜题的输入，以及第一天谜题的解决方案。

+   [第2天](https://medium.com/towards-data-science/im-doing-the-advent-of-code-2024-in-python-day-2-6d452447ba0f), [第3天](https://medium.com/towards-data-science/im-doing-the-advent-of-code-2024-in-python-day-3-3a3bdf845685)

在第4天的谜题中，我们将学习以下内容：

+   Python 中的列表推导式

+   如何处理一维和二维 NumPy 数组

+   如何转置和翻转 NumPy 数组

在写这篇文章时，已经发布了22个谜题，每个谜题有两个部分，每个部分都会获得一个星星，以下是我当前的进度：

![](../Images/0a0fd75fb9411f7a374467f6c443c6a3.png)

（图片由作者提供）

# 第4天 — 第1部分

第4天的谜题输入是一长串包含字母 X、M、A 和 S 的字符串。我的谜题输入的前10行如下所示：

![](../Images/ded849ef6cc364a8f29d98b37d5b3b37.png)

（图片由作者提供）
