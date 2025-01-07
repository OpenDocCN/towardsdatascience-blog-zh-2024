# 利用Gemini-1.5-Pro-Latest实现更智能的饮食

> 原文：[https://towardsdatascience.com/leveraging-gemini-1-5-pro-latest-for-smarter-eating-64eeb4012ce7?source=collection_archive---------8-----------------------#2024-08-21](https://towardsdatascience.com/leveraging-gemini-1-5-pro-latest-for-smarter-eating-64eeb4012ce7?source=collection_archive---------8-----------------------#2024-08-21)

## 学习如何使用Google的Gemini-1.5-pro-latest模型开发一个用于卡路里计数的生成式AI应用程序

[](https://medium.com/@aramary?source=post_page---byline--64eeb4012ce7--------------------------------)[![Mary Ara](../Images/d94dfcf5103be921d37069d344587d13.png)](https://medium.com/@aramary?source=post_page---byline--64eeb4012ce7--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--64eeb4012ce7--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--64eeb4012ce7--------------------------------) [Mary Ara](https://medium.com/@aramary?source=post_page---byline--64eeb4012ce7--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--64eeb4012ce7--------------------------------) ·阅读时长8分钟·2024年8月21日

--

![](../Images/88562e5f7ad208447f0cd8b060eba7cc.png)

图片由[Pickled Stardust](https://unsplash.com/@pickledstardust?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)提供，来源于[Unsplash](https://unsplash.com/photos/yellow-and-green-fruit-on-brown-weighing-scale-WTDiM3nezLQ?utm_content=creditCopyText&utm_medium=referral&utm_source=unsplash)

你有没有想过，在你吃晚餐时，你究竟摄入了多少卡路里？我经常这样想。如果你能通过一个应用程序，简单地上传一张你的盘子照片，就能在决定食物的摄入量之前，获得总卡路里的估算，那该有多好？

我创建的这个卡路里计数应用程序可以帮助你实现这一目标。它是一个Python应用程序，使用Google的Gemini-1.5-Pro-Latest模型来估算食物中的卡路里含量。

这个应用程序接受两个输入：一个关于食物的问题和一张食物或食物项的图片，或者简单地说，一盘食物。它会输出问题的答案、图片中食物的总卡路里数量，以及每个食物项的卡路里分解。

在本文中，我将解释从零开始构建应用程序的完整端到端过程，使用Google的Gemini-1.5-pro-latest（Google发布的大型生成式语言模型），以及我如何使用Streamlit开发该应用程序的前端。
