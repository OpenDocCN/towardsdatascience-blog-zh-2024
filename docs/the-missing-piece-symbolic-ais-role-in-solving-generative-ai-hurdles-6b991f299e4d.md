# 缺失的拼图：符号人工智能在解决生成式人工智能难题中的作用

> 原文：[`towardsdatascience.com/the-missing-piece-symbolic-ais-role-in-solving-generative-ai-hurdles-6b991f299e4d?source=collection_archive---------8-----------------------#2024-08-16`](https://towardsdatascience.com/the-missing-piece-symbolic-ais-role-in-solving-generative-ai-hurdles-6b991f299e4d?source=collection_archive---------8-----------------------#2024-08-16)

## 符号推理或许依然存在……

[](https://rafebrena.medium.com/?source=post_page---byline--6b991f299e4d--------------------------------)![Rafe Brena, Ph.D.](https://rafebrena.medium.com/?source=post_page---byline--6b991f299e4d--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6b991f299e4d--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6b991f299e4d--------------------------------) [Rafe Brena, Ph.D.](https://rafebrena.medium.com/?source=post_page---byline--6b991f299e4d--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6b991f299e4d--------------------------------) ·阅读时长 7 分钟 ·2024 年 8 月 16 日

--

![](img/1078d805f567bfcdecdf36051bfcdebf.png)

图片来源：[Arno Senoner](https://unsplash.com/@arnosenoner?utm_source=medium&utm_medium=referral) 于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

[幻觉](https://arxiv.org/abs/2311.05232)、事实错误、[公众兴趣下降](https://www.techfinitive.com/chatgpts-popularity-drops-say-new-figures/)、以及 [投资骤降](https://news.crunchbase.com/ai/early-stage-deal-making-falls-q2-2024-xai-coreweave/) ——所有这些以及更多内容，最近都在新闻和评论中被提到，似乎预示着生成式人工智能将面临严重的挫折。有些人甚至呼吁 AI “[泡沫](https://www.theguardian.com/commentisfree/2024/apr/13/from-boom-to-burst-the-ai-bubble-is-only-heading-in-one-direction)” 的终结，认为他们“早就预测到了这一点。”

在这篇文章中，我讨论了当前生成式人工智能系统的难题如何能够（或已经）通过老派的符号推理得到缓解。

但让我们首先解释一下当前的难题。

以基础的算术为例。昨天我问 ChatGPT，123,456.78 的立方根是多少，得到的答案是 49.3989。接着我又让 ChatGPT 把 49.3989 自己乘两次，假设能够回到我的 123,456.78 数字，结果——没开玩笑——ChatGPT 给出的结果还是 123,456.78，假装验证它的操作是正确的。我本可以相信它，但为了确保，我用计算器做了一遍运算，结果是 49.3989 自己乘两次得到的是 120,545.73，差不多偏离了 3,000 单位。这当然意味着，第一个操作（立方根）是错的……
