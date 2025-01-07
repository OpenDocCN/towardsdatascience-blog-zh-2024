# 用线性编程优化超级碗方格游戏

> 原文：[https://towardsdatascience.com/optimizing-super-bowl-squares-with-linear-programming-c33cc09ad0f8?source=collection_archive---------6-----------------------#2024-02-06](https://towardsdatascience.com/optimizing-super-bowl-squares-with-linear-programming-c33cc09ad0f8?source=collection_archive---------6-----------------------#2024-02-06)

## 一种灵感来源于数独的方式，旨在最小化竞争优势

[](https://sejaldua.medium.com/?source=post_page---byline--c33cc09ad0f8--------------------------------)[![Sejal Dua](../Images/b9ec1f4907da5e6dfa1c922caa5b326d.png)](https://sejaldua.medium.com/?source=post_page---byline--c33cc09ad0f8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--c33cc09ad0f8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--c33cc09ad0f8--------------------------------) [Sejal Dua](https://sejaldua.medium.com/?source=post_page---byline--c33cc09ad0f8--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--c33cc09ad0f8--------------------------------) ·10分钟阅读·2024年2月6日

--

超级碗星期天历来通过满足不同兴趣的观众，吸引了美国观众的关注。足球迷为赛场上的激烈动作而兴奋，而普通观众则被这场盛会所吸引——从引人注目的广告到炫目的中场秀。然而，如今的超级碗超越了传统的两类观众模式。例如，今年，泰勒·斯威夫特的忠实粉丝群体（“Swifties”）可能会调频观看她与特拉维斯·凯尔西的互动，因为堪萨斯城酋长队试图卫冕他们的冠军头衔。此外，随着法律体育博彩市场的蓬勃发展，再加上比赛在拉斯维加斯举行，为经验丰富和业余的投注者都带来了更多肾上腺素的刺激。

![](../Images/6fc0b1ba0a8ab8f32ee3069094e47078.png)

图片来源：Robert Hernandez Villalta：[https://www.pexels.com/photo/nfl-stadium-field-full-with-crowd-watching-the-game-during-daytime-128457/](https://www.pexels.com/photo/nfl-stadium-field-full-with-crowd-watching-the-game-during-daytime-128457/)

本文适用于那些了解足球一般规则，但更偏向于“好玩”的人，而非铁杆粉丝。如果你喜欢在家庭和/或朋友聚会时享受各种薯片和蘸酱，并且不介意以类似“宾果”游戏的方式为一年一度的超级碗比赛增添一些兴奋感，那么请允许我向你介绍超级碗方格游戏。

*作者注：如果你已经知道超级碗方格游戏的规则，可以跳过下一部分。*
