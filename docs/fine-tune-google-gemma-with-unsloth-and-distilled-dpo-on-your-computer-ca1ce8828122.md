# 在你的电脑上使用Unsloth和蒸馏DPO微调Google Gemma

> 原文：[https://towardsdatascience.com/fine-tune-google-gemma-with-unsloth-and-distilled-dpo-on-your-computer-ca1ce8828122?source=collection_archive---------5-----------------------#2024-03-18](https://towardsdatascience.com/fine-tune-google-gemma-with-unsloth-and-distilled-dpo-on-your-computer-ca1ce8828122?source=collection_archive---------5-----------------------#2024-03-18)

## 遵循Hugging Face的Zephyr配方

[](https://medium.com/@bnjmn_marie?source=post_page---byline--ca1ce8828122--------------------------------)[![Benjamin Marie](../Images/3ea1ad230cb1e67610418a8e36a5e5dd.png)](https://medium.com/@bnjmn_marie?source=post_page---byline--ca1ce8828122--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--ca1ce8828122--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--ca1ce8828122--------------------------------) [Benjamin Marie](https://medium.com/@bnjmn_marie?source=post_page---byline--ca1ce8828122--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--ca1ce8828122--------------------------------) ·阅读时间8分钟·2024年3月18日

--

![](../Images/b55099aa531dd1323734916dffb4496b.png)

由DALL-E生成

为新LLM模型寻找合适的训练超参数一直是一个既困难又耗时的任务。通过[Zephyr Gemma 7B](https://huggingface.co/HuggingFaceH4/zephyr-7b-gemma-v0.1)，Hugging Face似乎找到了一个良好的微调Gemma的配方。他们采用了蒸馏监督式微调与DPO的组合，类似于他们为[原版Zephyr（基于Mistral 7B）](https://medium.com/towards-data-science/zephyr-7b-beta-a-good-teacher-is-all-you-need-c931fcd0bfe7)所做的。然而，由于内存消耗问题，在消费者硬件上用DPO训练Gemma仍然具有挑战性。

本文首先回顾了Hugging Face用于训练Zephyr Gemma 7B的配方。接着，我展示了如何结合使用这个配方与Unsloth，一个实现了各种优化的框架，用于快速且内存高效的训练。本文中介绍的方法的峰值内存消耗为19GB VRAM，训练总时长仅为8小时。换句话说，在消费者硬件上进行Gemma的DPO训练是可行的。

# 深入了解Zephyr Gemma

## 监督式微调（SFT）

DPO必须参考一个已经通过在指令数据集上进行监督式微调（SFT）训练的模型。Hugging Face也发布了这个SFT模型：

+   [HuggingFaceH4/zephyr-7b-gemma-sft-v0.1](https://huggingface.co/HuggingFaceH4/zephyr-7b-gemma-sft-v0.1)
