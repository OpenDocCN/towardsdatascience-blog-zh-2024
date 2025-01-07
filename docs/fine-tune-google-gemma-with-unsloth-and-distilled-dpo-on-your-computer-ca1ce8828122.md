# 在你的电脑上使用 Unsloth 和蒸馏 DPO 微调 Google Gemma

> 原文：[`towardsdatascience.com/fine-tune-google-gemma-with-unsloth-and-distilled-dpo-on-your-computer-ca1ce8828122?source=collection_archive---------5-----------------------#2024-03-18`](https://towardsdatascience.com/fine-tune-google-gemma-with-unsloth-and-distilled-dpo-on-your-computer-ca1ce8828122?source=collection_archive---------5-----------------------#2024-03-18)

## 遵循 Hugging Face 的 Zephyr 配方

[](https://medium.com/@bnjmn_marie?source=post_page---byline--ca1ce8828122--------------------------------)![Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--ca1ce8828122--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ca1ce8828122--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ca1ce8828122--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--ca1ce8828122--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ca1ce8828122--------------------------------) ·阅读时间 8 分钟·2024 年 3 月 18 日

--

![](img/b55099aa531dd1323734916dffb4496b.png)

由 DALL-E 生成

为新 LLM 模型寻找合适的训练超参数一直是一个既困难又耗时的任务。通过[Zephyr Gemma 7B](https://huggingface.co/HuggingFaceH4/zephyr-7b-gemma-v0.1)，Hugging Face 似乎找到了一个良好的微调 Gemma 的配方。他们采用了蒸馏监督式微调与 DPO 的组合，类似于他们为[原版 Zephyr（基于 Mistral 7B）](https://medium.com/towards-data-science/zephyr-7b-beta-a-good-teacher-is-all-you-need-c931fcd0bfe7)所做的。然而，由于内存消耗问题，在消费者硬件上用 DPO 训练 Gemma 仍然具有挑战性。

本文首先回顾了 Hugging Face 用于训练 Zephyr Gemma 7B 的配方。接着，我展示了如何结合使用这个配方与 Unsloth，一个实现了各种优化的框架，用于快速且内存高效的训练。本文中介绍的方法的峰值内存消耗为 19GB VRAM，训练总时长仅为 8 小时。换句话说，在消费者硬件上进行 Gemma 的 DPO 训练是可行的。

# 深入了解 Zephyr Gemma

## 监督式微调（SFT）

DPO 必须参考一个已经通过在指令数据集上进行监督式微调（SFT）训练的模型。Hugging Face 也发布了这个 SFT 模型：

+   [HuggingFaceH4/zephyr-7b-gemma-sft-v0.1](https://huggingface.co/HuggingFaceH4/zephyr-7b-gemma-sft-v0.1)
