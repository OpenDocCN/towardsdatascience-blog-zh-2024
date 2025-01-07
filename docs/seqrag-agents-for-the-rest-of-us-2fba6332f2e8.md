# SeqRAG：为我们所有人服务的智能体

> 原文：[https://towardsdatascience.com/seqrag-agents-for-the-rest-of-us-2fba6332f2e8?source=collection_archive---------9-----------------------#2024-10-28](https://towardsdatascience.com/seqrag-agents-for-the-rest-of-us-2fba6332f2e8?source=collection_archive---------9-----------------------#2024-10-28)

## 利用顺序检索增强生成（SeqRAG）构建更快速、更可靠的智能体

[](https://araudaschl.medium.com/?source=post_page---byline--2fba6332f2e8--------------------------------)[![Adrian H. Raudaschl](../Images/19fa097523e03879cb19f60a9c00c743.png)](https://araudaschl.medium.com/?source=post_page---byline--2fba6332f2e8--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2fba6332f2e8--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2fba6332f2e8--------------------------------) [Adrian H. Raudaschl](https://araudaschl.medium.com/?source=post_page---byline--2fba6332f2e8--------------------------------)

·发表于[数据科学前沿](https://towardsdatascience.com/?source=post_page---byline--2fba6332f2e8--------------------------------) ·阅读时间：13分钟·2024年10月28日

--

![](../Images/cb08532b50b79422db32ce0ae774ef35.png)

SeqRAG的奇妙世界。插图由作者提供。

AI智能体在代表我们执行复杂任务方面具有巨大潜力。然而，尽管像OpenAI的o1推理基础模型，甚至是最近的Claude“计算机用户”功能等进展，显然，要在可靠、日常、实用的方式中实现这一潜力仍然充满挑战。事实上，这一直是一个挑战。

早在1997年，史蒂夫·乔布斯在1997年苹果开发者大会上被问到：“**我们如何才能让计算机为人类工作呢？**” 他承认当时AI智能体面临的挑战。

“现在把我们的未来寄托在智能体世界研究的成果上…将是愚蠢的，”乔布斯曾警告，并补充说，**“我认为，某一时刻，它们（智能体）将开始以我们无法想象的方式为我们做更多的事。” — 乔布斯 2007年**

他的反应值得一看，[点击这里](https://youtu.be/yQ16_YxLbB8?t=2265)观看。

![](../Images/e4bc815182ecc59b8c89c081418b71dd.png)

乔布斯在[1997年苹果开发者大会](https://youtu.be/yQ16_YxLbB8?si=_YRNdV2jNzVixGmF&t=2265)上思考智能体解决方案。

目前，通往可靠的AI智能体的道路是一个[激动人心的领域](https://trends.google.com/trends/explore?date=today+5-y&q=AI+agents&hl=en-GB)，特别是随着研究人员不断找到制作更强大智能体的方法，这些智能体能够解决...
