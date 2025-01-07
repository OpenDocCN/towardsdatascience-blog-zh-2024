# FanFabler: 将Llama 3调整为多语言同人创作助手

> 原文：[https://towardsdatascience.com/fanfabler-fine-tuning-llama-3-to-be-a-multilingual-fanfic-writing-assistant-dfc664ed4a72?source=collection_archive---------3-----------------------#2024-05-07](https://towardsdatascience.com/fanfabler-fine-tuning-llama-3-to-be-a-multilingual-fanfic-writing-assistant-dfc664ed4a72?source=collection_archive---------3-----------------------#2024-05-07)

## 我如何使用自定义训练数据集和信息检索进行全球叙事。好样的！Bravo！वाह！¡Guau！브라보！

[](https://robgon.medium.com/?source=post_page---byline--dfc664ed4a72--------------------------------)[![Robert A. Gonsalves](../Images/96b4da0f602a1cd9d1e1d2917868cbee.png)](https://robgon.medium.com/?source=post_page---byline--dfc664ed4a72--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--dfc664ed4a72--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--dfc664ed4a72--------------------------------) [Robert A. Gonsalves](https://robgon.medium.com/?source=post_page---byline--dfc664ed4a72--------------------------------)

·发表在[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--dfc664ed4a72--------------------------------)·21分钟阅读·2024年5月7日

--

![](../Images/25eeb2a66bdf45b1cd5b94d5e6790f45.png)

**FanFabler: 多语言同人创作助手**，图像使用AI图像生成程序DALL-E 3创建，由作者编辑

大型语言模型（LLMs）的崛起开启了基于文本的人工智能系统的新时代。尽管这些模型非常优秀且功能强大，但它们的训练主要集中在英语上。最大的商业LLMs使用“低资源”语言生成文本效果很好，而较小的开源模型在非欧洲语言上表现不佳。

然而，Meta用更广泛的语言训练了新的Llama 3模型，正如他们在发布时在一篇[文章](https://ai.meta.com/blog/meta-llama-3/)中宣布的那样。

> 要训练最佳语言模型，策划一个大规模、高质量的训练数据集至关重要。根据我们的设计原则，我们在预训练数据上投入了大量资金。... 为了为即将到来的多语言用例做准备，Llama 3的超过5%预训练数据集包含覆盖30多种语言的高质量非英语数据。然而，我们不指望在这些语言中达到与英语相同水平的表现。 — Meta

五个百分点听起来不算多，但比Llama的先前版本[2]和其他小型LLMs如Mistral [3]更多...
