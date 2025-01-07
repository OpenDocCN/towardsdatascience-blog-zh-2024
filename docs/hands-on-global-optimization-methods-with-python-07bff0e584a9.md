# 使用 Python 进行全球优化方法实践

> 原文：[https://towardsdatascience.com/hands-on-global-optimization-methods-with-python-07bff0e584a9?source=collection_archive---------2-----------------------#2024-09-04](https://towardsdatascience.com/hands-on-global-optimization-methods-with-python-07bff0e584a9?source=collection_archive---------2-----------------------#2024-09-04)

## 四种方法用来寻找你黑箱目标函数的最大值（或最小值）。

[](https://piero-paialunga.medium.com/?source=post_page---byline--07bff0e584a9--------------------------------)[![Piero Paialunga](../Images/de2185596a49484698733e85114dd1ff.png)](https://piero-paialunga.medium.com/?source=post_page---byline--07bff0e584a9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--07bff0e584a9--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--07bff0e584a9--------------------------------) [Piero Paialunga](https://piero-paialunga.medium.com/?source=post_page---byline--07bff0e584a9--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--07bff0e584a9--------------------------------) ·阅读时间 13 分钟·2024年9月4日

--

![](../Images/640058c58432e67af93c1853b0e8d10f.png)

图片由作者使用 DALL·E 制作

想象一下和你的好朋友一起出去。你决定去一个你从未去过的地方，但你的好朋友曾经去过。

还要想象一下，你们所在的城市有些交通问题，比如***罗马***（我知道，因为我就是那里的人，真的很痛苦），并且你和你的好朋友会在**那里**见面，但是你们从**同一座房子**出发，各自开着两辆车。

你和你的好朋友**同时离开家**，由于你从未去过那个地方，你使用**GPS**来到达那里。

当你到达那里时，你发现你的朋友已经在点饮料，并告诉你他已经等你10分钟了。假设你和你的朋友都没有超速，这个故事告诉我们两件事：

1.  你应该生气于你的好朋友，因为他本可以告诉你更快的路线，而不是让你使用GPS。为什么他当时没有打电话给你呢？ 😡

1.  你刚刚遇到了一个**局部最小值问题**。

在这篇文章中，我们将专注于***问题二***，其余的就交给你自己去处理你和你朋友的事情吧。
