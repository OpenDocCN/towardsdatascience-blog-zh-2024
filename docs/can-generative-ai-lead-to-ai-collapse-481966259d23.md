# 生成性AI能引发AI崩溃吗？

> 原文：[https://towardsdatascience.com/can-generative-ai-lead-to-ai-collapse-481966259d23?source=collection_archive---------6-----------------------#2024-07-30](https://towardsdatascience.com/can-generative-ai-lead-to-ai-collapse-481966259d23?source=collection_archive---------6-----------------------#2024-07-30)

## |LLM|生成性AI|模型崩溃|

## AI吞噬自己的尾巴：生成系统中的模型崩溃风险

[](https://salvatore-raieli.medium.com/?source=post_page---byline--481966259d23--------------------------------)[![Salvatore Raieli](../Images/6bb4520e2df40d20283e7283141b5e06.png)](https://salvatore-raieli.medium.com/?source=post_page---byline--481966259d23--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--481966259d23--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--481966259d23--------------------------------) [Salvatore Raieli](https://salvatore-raieli.medium.com/?source=post_page---byline--481966259d23--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--481966259d23--------------------------------) ·阅读时长9分钟·2024年7月30日

--

![](../Images/b8b486423beca38a2f4ae4b4a4db6a7c.png)

这张图片由作者使用AI生成

> “文明的灭亡来自自杀，而非谋杀。” — 阿诺德·汤因比

[大规模语言模型（LLMs）](https://github.com/SalvatoreRa/tutorial/blob/main/artificial%20intelligence/FAQ.md#:~:text=What%20is%20a%20Large%20Language%20Model%20(LLM)%3F)通常是在大量文本数据上以无监督方式进行训练的。这些文本通过爬取互联网获得。虽然这些文本最初是由人类编写的，但这种情况很快可能会发生变化。

[](/a-requiem-for-the-transformer-297e6f14e189?source=post_page-----481966259d23--------------------------------) [## 《变换器的安魂曲》？

### 变换器模型是否将引领我们走向人工通用智能？还是会被替代？

towardsdatascience.com](/a-requiem-for-the-transformer-297e6f14e189?source=post_page-----481966259d23--------------------------------)

LLMs本质上是数据饥渴型的，而且用于训练的数据集越来越大。根据[扩展法则](https://github.com/SalvatoreRa/tutorial/blob/main/artificial%20intelligence/FAQ.md#:~:text=What%20does%20it%20mean%20emergent%20properties%3F%20what%20it%20is%20the%20scaling%20law%3F) [2]，为了提高性能，必须增加模型的参数数量和训练标记的数量（后者被认为是最重要的因素）。

这些数据集包含由人类生成的数据，然而，一些研究表明这是一种有限的资源。人类生成的数据量也不像我们这样庞大，因为我们正在通过[LLM训练](https://venturebeat.com/ai/ais-hunger-games-a-lucrative-data-market-is-exploding-to-feed-insatiable-llms-the-ai-beat/)增加数据消耗。一项研究…
