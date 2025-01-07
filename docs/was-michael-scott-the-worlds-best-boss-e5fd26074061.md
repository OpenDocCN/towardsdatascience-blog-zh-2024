# 迈克尔·斯科特是世界上最好的老板吗？

> 原文：[`towardsdatascience.com/was-michael-scott-the-worlds-best-boss-e5fd26074061?source=collection_archive---------4-----------------------#2024-07-29`](https://towardsdatascience.com/was-michael-scott-the-worlds-best-boss-e5fd26074061?source=collection_archive---------4-----------------------#2024-07-29)

## 使用 SchrutePy、NLTK 和 Hugging Face Transformers 🤗 对《办公室》电视剧进行情感分析

[](https://medium.com/@m.mouschoutzi?source=post_page---byline--e5fd26074061--------------------------------)![Maria Mouschoutzi, PhD](https://medium.com/@m.mouschoutzi?source=post_page---byline--e5fd26074061--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--e5fd26074061--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e5fd26074061--------------------------------) [Maria Mouschoutzi, PhD](https://medium.com/@m.mouschoutzi?source=post_page---byline--e5fd26074061--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--e5fd26074061--------------------------------) ·15 分钟阅读·2024 年 7 月 29 日

--

![](img/ea71488bb73406bf8727b937faf6732e.png)

图片来自 [Anthony A](https://unsplash.com/@antyn?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# 《办公室》怎么样？

当我年轻的时候，我总是听说*《办公室》*，并且在互联网上经常看到剧中的片段。我决定尝试一下——毕竟看起来是一部很酷的节目——但是由于当时在大学，没有办公室工作经验，我没能理解其中的内容，结果在看了几集之后就放弃了。几年后，在我进入企业办公室工作一段时间后，我在 Netflix 上随机发现了它，决定再试一次。我简直被迷住了！看了几集之后，我甚至觉得自己好像也在为 Dunder Mifflin 工作。每个角色都让人感到亲切，仿佛自己也曾经历过那种每周 9 到 5 的普遍焦虑，看到这一点时，我感到一丝宽慰。太喜欢了！

然而，迈克尔总是让我感到不舒服。我无法理解他。他看起来傻乎乎的，经常惹恼大家，但我们并不真正讨厌他，因为他内心深处是个甜蜜而善良的人。如果你仔细想想，剧中的每个人都有点像个混蛋。迈克尔、德怀特和安吉拉肯定是这样，但随着时间的推移，你也会意识到吉姆也有点烦人，甚至是菲莉丝或帕姆也有些让人心烦…
