# Spotify 是如何实现个性化有声书推荐的？

> 原文：[`towardsdatascience.com/how-spotify-implemented-personalized-audiobook-recommendations-09386a93ace2?source=collection_archive---------9-----------------------#2024-11-22`](https://towardsdatascience.com/how-spotify-implemented-personalized-audiobook-recommendations-09386a93ace2?source=collection_archive---------9-----------------------#2024-11-22)

## 使用图神经网络的个性化有声书推荐

[](https://saankhya.medium.com/?source=post_page---byline--09386a93ace2--------------------------------)![Saankhya Mondal](https://saankhya.medium.com/?source=post_page---byline--09386a93ace2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--09386a93ace2--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--09386a93ace2--------------------------------) [Saankhya Mondal](https://saankhya.medium.com/?source=post_page---byline--09386a93ace2--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--09386a93ace2--------------------------------) ·8 分钟阅读·2024 年 11 月 22 日

--

# 介绍

Spotify 是全球最受欢迎的音乐流媒体应用程序。除了歌曲和专辑，Spotify 还拥有大量播客和脱口秀节目。最近，他们在应用中推出了有声书。和其他产品一样，Spotify 希望确保其有声书推荐符合用户的偏好。因此，他们开发了一种基于图神经网络的推荐算法来个性化有声书推荐。

本文讨论了 Spotify 在提供个性化有声书推荐时面临的挑战，以及为解决这些挑战所进行的数据探索分析。它探讨了 Spotify 的创新解决方案：一个双塔图神经网络模型，旨在增强有声书的个性化推荐。

![](img/38fa54e1eeaec57a308dd1e51bf653a0.png)

图片由[Jukka Aalho](https://unsplash.com/@jukkaaalho?utm_source=medium&utm_medium=referral)提供，来源于[Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

## 挑战

由于有声书是 Spotify 内容库中的新成员，他们面临了一些挑战——

1.  由于有声书是新近推出的内容类型，数据稀缺成为一个问题。与其他内容类型相比，有声书的用户互动较少。许多用户甚至不知道 Spotify 有提供有声书。
