# 顺序测试：低量 A/B 测试的秘密武器

> 原文：[`towardsdatascience.com/sequential-testing-the-secret-sauce-for-low-volume-a-b-tests-fe62bdf9627b?source=collection_archive---------4-----------------------#2024-08-29`](https://towardsdatascience.com/sequential-testing-the-secret-sauce-for-low-volume-a-b-tests-fe62bdf9627b?source=collection_archive---------4-----------------------#2024-08-29)

## 如何在处理有限数据时加速决策并提高准确性

[](https://medium.com/@raicik.zach?source=post_page---byline--fe62bdf9627b--------------------------------)![Zachary Raicik](https://medium.com/@raicik.zach?source=post_page---byline--fe62bdf9627b--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--fe62bdf9627b--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fe62bdf9627b--------------------------------) [Zachary Raicik](https://medium.com/@raicik.zach?source=post_page---byline--fe62bdf9627b--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--fe62bdf9627b--------------------------------) ·阅读时间：7 分钟·2024 年 8 月 29 日

--

![](img/c0a9b9d2c030bd3b79d9736bd0d36c08.png)

图片由 OpenAI 的 chatGPT 生成

# **什么是 A/B 测试，为什么它很难？**

A/B 测试是一种通过提供基于数据的方式来确定哪个版本的产品更有效，从而减少决策不确定性的简单方法。A/B 测试的概念很简单。

+   假设你在朋友的生日派对上。你一直在精心完善你的饼干食谱。你认为你已经完美了，但你不确定大家是更喜欢含燕麦的饼干，还是不含燕麦的饼干。在你看来，燕麦让饼干有一种很好的嚼劲。然而，你不确定这是否是大众的看法，还是仅仅是你个人的偏好。

+   最终你带着两种不同版本的饼干出现在派对上，A 饼干含有燕麦，而 B 饼干没有。你随机给一半的朋友 A 饼干，另一半则得到 B 饼干。

+   你决定，得到更多“赞”的饼干就是更好的饼干。

+   一旦每个人都尝试了这些饼干，你发现 B 饼干得到了更多的“赞”，于是你得出结论，B 饼干是更好的饼干。
