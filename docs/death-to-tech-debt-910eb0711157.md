# 技术债务的终结？

> 原文：[https://towardsdatascience.com/death-to-tech-debt-910eb0711157?source=collection_archive---------14-----------------------#2024-07-31](https://towardsdatascience.com/death-to-tech-debt-910eb0711157?source=collection_archive---------14-----------------------#2024-07-31)

## 深入探讨AI驱动的技术债务减少方法以及剩余的技术限制。

[](https://arijoury.medium.com/?source=post_page---byline--910eb0711157--------------------------------)[![Ari Joury, PhD](../Images/5b9e49279fb3f26373b393f29a4daaf7.png)](https://arijoury.medium.com/?source=post_page---byline--910eb0711157--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--910eb0711157--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--910eb0711157--------------------------------) [Ari Joury, PhD](https://arijoury.medium.com/?source=post_page---byline--910eb0711157--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--910eb0711157--------------------------------) ·13分钟阅读·2024年7月31日

--

![](../Images/f9859f825ed545414f549daf8e0febc7.png)

死神可能正在逼近技术债务。图片由[Leonardo.ai](https://app.leonardo.ai/image-generation)生成

**本文由** [**David Meiborg**](https://medium.com/u/8523370997f4?source=post_page---user_mention--910eb0711157--------------------------------) **共同撰写。**

*我们在与一位经验丰富的技术高管合作过程中，创建了这篇深入分析文章。他希望在“AI将解决技术债务”领域建立一个创业公司。现在，他仍在寻找团队成员加入他的创业项目——如果你感兴趣，欢迎联系！*

# 定义技术债务

技术债务，根据[Gartner](https://www.gartner.com/en/information-technology/glossary/technical-debt)的定义，是由于在软件开发过程中采取了捷径和做出牺牲所导致的“欠”IT系统的累积工作。这些妥协通常是为了满足交付期限而必须做出的，但可能导致软件的非功能性需求出现偏差，最终影响性能、可扩展性和韧性。

Gartner还强调，尽管传统应用程序基于过时的技术，但它们通常对于日常运营至关重要。当这些系统发生故障时，后果可能非常严重，带来诸如代码复杂性、高开发成本以及安全漏洞风险等挑战，甚至可能使最具创新性的公司陷入瘫痪。

技术债务可以在领导者将快速进展置于最优解之上时有意产生...
