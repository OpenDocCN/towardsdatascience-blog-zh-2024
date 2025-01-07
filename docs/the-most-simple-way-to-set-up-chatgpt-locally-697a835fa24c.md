# 在本地设置ChatGPT的最简单方法

> 原文：[https://towardsdatascience.com/the-most-simple-way-to-set-up-chatgpt-locally-697a835fa24c?source=collection_archive---------3-----------------------#2024-01-17](https://towardsdatascience.com/the-most-simple-way-to-set-up-chatgpt-locally-697a835fa24c?source=collection_archive---------3-----------------------#2024-01-17)

## 在消费者硬件上运行LLM的秘诀

[](https://dennisbakhuis.medium.com/?source=post_page---byline--697a835fa24c--------------------------------)[![Dennis Bakhuis](../Images/4dc6dca031cdedbb044a1d0a6b142186.png)](https://dennisbakhuis.medium.com/?source=post_page---byline--697a835fa24c--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--697a835fa24c--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--697a835fa24c--------------------------------) [Dennis Bakhuis](https://dennisbakhuis.medium.com/?source=post_page---byline--697a835fa24c--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--697a835fa24c--------------------------------) ·阅读时长10分钟·2024年1月17日

--

![](../Images/4e88f8fdb92a29a3ddbbbae01f561100.png)

图1：可爱的小机器人们正在未来派的肥皂工厂工作（[unsplash: Gerard Siderius](https://unsplash.com/photos/a-robot-holding-a-gun-next-to-a-pile-of-rolls-of-toilet-paper-YeoSV_3Up-k)）。

作为一名数据科学家，我已经花费了大量时间深入研究像[BERT](https://arxiv.org/abs/1810.04805)、GPT{[2](https://openai.com/research/better-language-models),[3](https://arxiv.org/abs/2005.14165),[4](https://arxiv.org/abs/2303.08774)}和[ChatGPT](https://openai.com/blog/chatgpt)这样的超大语言模型（LLMs）的复杂性。这些先进的模型在规模上有了显著扩展，使得在标准消费者设备上运行最新的高性能模型变得越来越具有挑战性。遗憾的是，在我家，我仍然没有8x A100机器可供使用。

> 我（目前）家里没有8x A100机器

在过去几年里，一种新的技术被用来使模型变得更小更快：量化。这种方法巧妙地将曾经庞大的LLM缩小到一个更*易于消化*的大小，适合消费者级硬件。它就像是让这些人工智能巨头进行数字化减肥，使它们能够舒适地适应我们家用计算机更为谦逊的空间。与此同时，开源社区，像🤗 HuggingFace和🦄 Mistral这样的先行者，在普及这些模型方面发挥了重要作用。他们基本上将这个原本排外的AI俱乐部变成了一个‘人人欢迎’的科技盛会——[无需秘密握手](https://www.youtube.com/watch?v=HoibVdUAYkw)!

尽管经过指令训练的模型权重是谜题中的重要一环，但它们并不是全部。可以将这些权重看作是操作的“大脑”——…
