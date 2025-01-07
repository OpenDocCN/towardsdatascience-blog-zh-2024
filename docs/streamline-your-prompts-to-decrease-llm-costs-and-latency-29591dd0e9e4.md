# 精简你的提示语，降低 LLM 的成本和延迟

> 原文：[`towardsdatascience.com/streamline-your-prompts-to-decrease-llm-costs-and-latency-29591dd0e9e4?source=collection_archive---------5-----------------------#2024-05-24`](https://towardsdatascience.com/streamline-your-prompts-to-decrease-llm-costs-and-latency-29591dd0e9e4?source=collection_archive---------5-----------------------#2024-05-24)

## 探索 5 种优化令牌使用的技巧，同时不牺牲准确性

[](https://medium.com/@janekmajewski?source=post_page---byline--29591dd0e9e4--------------------------------)![Jan Majewski](https://medium.com/@janekmajewski?source=post_page---byline--29591dd0e9e4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--29591dd0e9e4--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--29591dd0e9e4--------------------------------) [Jan Majewski](https://medium.com/@janekmajewski?source=post_page---byline--29591dd0e9e4--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--29591dd0e9e4--------------------------------) ·8 分钟阅读·2024 年 5 月 24 日

--

![](img/7acbcaeb3816dbcac95c78644fc0bad3.png)

图片由作者使用 GPT-4o 生成

# 高成本和延迟是将 LLM 应用投入生产时的主要障碍之一，这与提示语的大小密切相关

大型语言模型的多功能性使得许多人认为它们可以作为几乎任何任务的“万能钥匙”。当你将 LLM 与包括 RAG 和 API 调用在内的工具结合，并给予它们详细指令时，它们常常能够在接近人类的水平上执行任务。

这种一体化方法的关键问题可能很快导致**提示语的爆炸性增长，从而导致每次调用的成本和延迟变得不可行。**

如果你使用 LLM 来优化像编程这样高价值的活动，保持低成本并不是首要任务——你可以等待半分钟来获取你通常需要花半小时编写的代码。然而，当在面向客户的应用中使用 LLM 时，尤其是在预期会有成千上万的聊天交互时，成本和延迟可能会决定你的解决方案能否成功。

本文是系列文章中的第一篇，在其中我将分享我为[Resider.pl](http://www.Resider.pl)构建 LLM 驱动的房地产搜索助手‘*Mieszko*’的经验和见解…
