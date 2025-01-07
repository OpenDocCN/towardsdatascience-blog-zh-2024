# 眼见为实 — 深度伪造及其如何扭曲真相

> 原文：[`towardsdatascience.com/seeing-is-believing-deepfakes-and-how-they-warp-truth-38a59af51562?source=collection_archive---------7-----------------------#2024-03-20`](https://towardsdatascience.com/seeing-is-believing-deepfakes-and-how-they-warp-truth-38a59af51562?source=collection_archive---------7-----------------------#2024-03-20)

## 桥接自编码器与媒介素养

[](https://medium.com/@ebbeberge?source=post_page---byline--38a59af51562--------------------------------)![Eirik Berge, PhD](https://medium.com/@ebbeberge?source=post_page---byline--38a59af51562--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--38a59af51562--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--38a59af51562--------------------------------) [Eirik Berge, PhD](https://medium.com/@ebbeberge?source=post_page---byline--38a59af51562--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--38a59af51562--------------------------------) ·阅读时间 9 分钟·2024 年 3 月 20 日

--

![](img/ed3da33b9cbc2d5441e47b9d8660de98.png)

图片由[Brett Jordan](https://unsplash.com/@brett_jordan?utm_source=medium&utm_medium=referral)提供，来自[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 概述

1.  介绍 — 什么是深度伪造？

1.  恶意深度伪造的例子

1.  自编码器

1.  媒介素养与深度伪造的检测

1.  总结

# 介绍 — 什么是深度伪造？

**照片操控**的行为由来已久。它曾用于为第一次世界大战的旧照片上色，也曾被用于宣传工具；约瑟夫·斯大林臭名昭著地操控照片，使得他的政治对手列昂·托洛茨基从重要场合中消失。照片操控已经有超过 100 年的历史，既用来吸引观众，也用来欺骗观众。

随着时间的推移，我们在日常生活中不仅消费图片，还消费视频和音频。互联网促进了视频和音频分享的急剧增长，个人和组织的分享量远远超过过去固定的电视和广播频道。这对获得新视角是很有帮助的。然而，这也与人工智能的创新一起，带来了一个新的可怕概念：**深度伪造**。
