# Jamba：全新的混合型变换器/马姆巴模型

> 原文：[`towardsdatascience.com/jamba-the-new-hybrid-transformer-mamba-f18ee6ce0768?source=collection_archive---------10-----------------------#2024-04-30`](https://towardsdatascience.com/jamba-the-new-hybrid-transformer-mamba-f18ee6ce0768?source=collection_archive---------10-----------------------#2024-04-30)

## 比变换器更快、更好，但训练起来更为困难

[](https://medium.com/@bnjmn_marie?source=post_page---byline--f18ee6ce0768--------------------------------)![Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--f18ee6ce0768--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f18ee6ce0768--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f18ee6ce0768--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--f18ee6ce0768--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f18ee6ce0768--------------------------------) ·阅读时长 8 分钟·2024 年 4 月 30 日

--

![](img/16dcfaa72d390c8b886f9384b4b5448c.png)

使用 DALL-E 生成

变换器神经网络架构是最先进的。它扩展性极好，即更大的模型能学得更好，并且由于注意力机制的并行计算，训练起来也很高效。

然而，变换器也有一些缺点，尤其是在推理阶段。注意力的计算成本随着序列长度的增加而呈二次方增长。为了缓解这一成本，已经提出了许多技术，比如 Alibi 和 RoPE。

也有一些替代的神经网络架构被提出，比如 RWKV 和 Mamba，它们是无注意力机制的状态空间模型（SSM）。与变换器相比，它们在推理时效率更高，但在准确性方面仍有所欠缺。

为了同时利用变换器和 SSM 架构，提出了 Jamba。这个混合模型结合了 SSM 和变换器层。这种结合使得在内存使用、训练效率和长上下文能力之间实现了平衡。

[Jamba: 一种混合变换器-马姆巴语言模型](https://arxiv.org/abs/2403.19887)

Jamba 的表现与 Mixtral-7x8B（最好的开放式大型语言模型之一）相当，但它更高效，特别是在处理长上下文时。
