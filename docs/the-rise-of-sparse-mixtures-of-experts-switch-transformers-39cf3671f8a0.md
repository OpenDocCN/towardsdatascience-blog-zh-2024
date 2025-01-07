# 稀疏专家混合的崛起：Switch Transformers

> 原文：[`towardsdatascience.com/the-rise-of-sparse-mixtures-of-experts-switch-transformers-39cf3671f8a0?source=collection_archive---------6-----------------------#2024-02-15`](https://towardsdatascience.com/the-rise-of-sparse-mixtures-of-experts-switch-transformers-39cf3671f8a0?source=collection_archive---------6-----------------------#2024-02-15)

## 深入探讨为今天行业中最强大的大规模语言模型（LLM）铺平道路的技术

[](https://medium.com/@samuel.flender?source=post_page---byline--39cf3671f8a0--------------------------------)![Samuel Flender](https://medium.com/@samuel.flender?source=post_page---byline--39cf3671f8a0--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--39cf3671f8a0--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--39cf3671f8a0--------------------------------) [Samuel Flender](https://medium.com/@samuel.flender?source=post_page---byline--39cf3671f8a0--------------------------------)

· 发表在 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--39cf3671f8a0--------------------------------) ·8 分钟阅读·2024 年 2 月 15 日

--

![](img/f8e64759f9946f38accbec2ec3e09a23.png)

使用 Dall-E 生成的图像

稀疏专家混合（MoE）已成为最新一代大规模语言模型（LLM）中的关键技术，如 OpenAI 的 GPT-4、Mistral AI 的 Mixtral-8x7 等。简而言之，稀疏 MoE 是一种极其强大的技术，因为——从理论上讲——它使我们能够以 O(1) 的计算复杂度扩展*任何*模型的容量！

然而，正如通常所说，问题往往隐藏在细节中，正确实现稀疏 MoE 需要精确掌握这些细节。

在这篇文章中，我们将深入探讨稀疏专家混合（MoE）领域的一个重要贡献——Switch Transformer（[Fedus et al 2022](https://arxiv.org/abs/2101.03961)），这是首次展示了使用这一技术可以实现的惊人扩展性，成功地在训练 Transformer 模型时达到了 7 倍的加速。我们将涵盖：

+   硬路由：通过为每个 token 执行单一专家来获得有利的扩展性特性，

+   Switch Transformer 架构：MoE 如何融入 Transformer 架构的更广泛背景，

+   token 路由动态：如何利用容量因子进行权衡……
