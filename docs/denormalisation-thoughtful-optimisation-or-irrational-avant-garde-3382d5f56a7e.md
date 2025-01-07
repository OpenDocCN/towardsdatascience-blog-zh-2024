# 非规范化：深思熟虑的优化还是不理性的先锋派？

> 原文：[https://towardsdatascience.com/denormalisation-thoughtful-optimisation-or-irrational-avant-garde-3382d5f56a7e?source=collection_archive---------2-----------------------#2024-08-10](https://towardsdatascience.com/denormalisation-thoughtful-optimisation-or-irrational-avant-garde-3382d5f56a7e?source=collection_archive---------2-----------------------#2024-08-10)

## **关于性能优化和数据质量的视角**

[](https://blog.heilmela.eu/?source=post_page---byline--3382d5f56a7e--------------------------------)[![Laurin Heilmeyer](../Images/bf503815c37deba7a84ae313530f20e6.png)](https://blog.heilmela.eu/?source=post_page---byline--3382d5f56a7e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3382d5f56a7e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--3382d5f56a7e--------------------------------) [Laurin Heilmeyer](https://blog.heilmela.eu/?source=post_page---byline--3382d5f56a7e--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3382d5f56a7e--------------------------------) ·15分钟阅读·2024年8月10日

--

![](../Images/aa951d0881ddb3c14ee92555b68ce310.png)

拆解关系型数据 - （图片来源：[DALL-E](https://openai.com/index/dall-e-2/)）

这对一些人来说可能会很惊讶：数据建模通常是一个协作的过程，涉及来自不同领域的人的激烈辩论。这是一个典型的孕育异想天开观点和巧妙技巧的土壤，使其成为关于最佳方法辩论的经典话题。为了让这个话题更具戏剧性，我喜欢把它看作是一个灰胡子纯粹主义者，手握厚厚的规则手册，与一个轻率的先锋派人物之间的对抗，后者对一切都抛出华丽的*NoSQL*（无论这意味着什么）。

拥有一套规则是有帮助的，因为很难理解所有决策的影响，特别是面对一个新问题时。经验在掌握任何学科中都起着至关重要的作用，数据建模也不例外。既定的规则和惯例作为桥梁，弥补了知识的鸿沟。然而，在遵循这些准则与开放接纳经验和务实推理之间，存在着微妙的平衡。

> “要深刻理解规则，这样你才能有效地打破它们。”
> 
> *达赖喇嘛的18条生活规则*

在我看来，这句格言确实非常精准。我很重视做出有根据的决策，而在与传统规则推理时……
