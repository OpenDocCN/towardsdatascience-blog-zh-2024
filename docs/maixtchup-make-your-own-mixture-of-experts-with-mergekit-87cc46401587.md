# Maixtchup：使用 Mergekit 创建你自己的专家混合模型

> 原文：[https://towardsdatascience.com/maixtchup-make-your-own-mixture-of-experts-with-mergekit-87cc46401587?source=collection_archive---------11-----------------------#2024-01-29](https://towardsdatascience.com/maixtchup-make-your-own-mixture-of-experts-with-mergekit-87cc46401587?source=collection_archive---------11-----------------------#2024-01-29)

## MoE 的崛起

[](https://medium.com/@bnjmn_marie?source=post_page---byline--87cc46401587--------------------------------)[![Benjamin Marie](../Images/3ea1ad230cb1e67610418a8e36a5e5dd.png)](https://medium.com/@bnjmn_marie?source=post_page---byline--87cc46401587--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--87cc46401587--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--87cc46401587--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--87cc46401587--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--87cc46401587--------------------------------) ·阅读时长：8分钟·2024年1月29日

--

![](../Images/ce341668f026e81e27282ec3085bba1a.png)

作者提供的图片 — 通过 DALL-E 生成

自从 Mistral AI 发布 Mixtral-8x7B 以来，业界对 [专家混合模型（MoE）](https://medium.com/p/0e3fc7fde818)的兴趣重新高涨。这种架构利用专家子网络，在推理过程中只有部分子网络被路由网络选择并激活。

MoE（专家混合模型）非常简单且灵活，便于制作自定义 MoE。在 Hugging Face Hub 上，我们现在可以找到几种流行的自定义 MoE LLM（大型语言模型），例如 [mlabonne/phixtral-4x2_8](https://huggingface.co/mlabonne/phixtral-4x2_8)。

然而，大多数模型并不是从头开始构建的传统 MoE，它们只是将已经微调过的 LLM 作为专家进行组合。它们的创建过程通过 [mergekit](https://github.com/cg123/mergekit)（[LGPL-3.0 许可证](https://github.com/cg123/mergekit#LGPL-3.0-1-ov-file)）变得更加简便。例如，Phixtral LLM 就是通过 mergekit 将多个 [Phi-2 模型](https://medium.com/p/06db49949ff1)结合而成的。

在本文中，我们将展示 Phixtral 是如何创建的。我们将应用相同的过程，使用多个 Mistral 7B 模型来创建我们自己的专家混合模型，Maixtchup。

# 什么是 Phixtral？

为了快速理解模型的高层架构，我喜欢将其打印出来。例如，对于 [mlabonne/phixtral-4x2_8](https://huggingface.co/mlabonne/phixtral-4x2_8)（MIT 许可证）：

```py
from transformers import AutoModelForCausalLM
model = AutoModelForCausalLM.from_pretrained(…
```
