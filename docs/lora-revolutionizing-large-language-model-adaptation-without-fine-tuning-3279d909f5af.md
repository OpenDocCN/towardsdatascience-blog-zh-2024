# LoRA：通过不进行微调，彻底改变大型语言模型的适配方式

> 原文：[`towardsdatascience.com/lora-revolutionizing-large-language-model-adaptation-without-fine-tuning-3279d909f5af?source=collection_archive---------8-----------------------#2024-04-23`](https://towardsdatascience.com/lora-revolutionizing-large-language-model-adaptation-without-fine-tuning-3279d909f5af?source=collection_archive---------8-----------------------#2024-04-23)

## 利用微调过程中权重更新的低秩特性，可以将可学习参数的数量减少数量级。

[](https://medium.com/@samuel.flender?source=post_page---byline--3279d909f5af--------------------------------)![Samuel Flender](https://medium.com/@samuel.flender?source=post_page---byline--3279d909f5af--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3279d909f5af--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3279d909f5af--------------------------------) [Samuel Flender](https://medium.com/@samuel.flender?source=post_page---byline--3279d909f5af--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3279d909f5af--------------------------------) ·阅读时间：8 分钟·2024 年 4 月 23 日

--

![](img/0fdea0772d1307bd66eb057e35a2c984.png)

图片由 ChatGPT 生成

自从 2019 年引入 BERT 以来，[微调](https://mlfrontiers.substack.com/p/what-exactly-happens-when-we-fine)一直是将大型语言模型（LLM）适配到下游任务的标准方法。随着 LoRA 的引入，这一情况发生了变化（[Hu 等人 2021](https://arxiv.org/abs/2106.09685)），它首次展示了通过低秩分解，可以大幅简化微调过程中权重更新矩阵，通常会减少数量级的可训练参数！

LoRA 在 NLP 社区中非常受欢迎，因为它使我们能够比以往更快、更稳定地将 LLM 适配到下游任务，并且模型的占用空间更小。

让我们来看看它是如何工作的。

## 微调的问题

谷歌的 BERT（[Devlin 等人 2019](https://arxiv.org/pdf/1810.04805.pdf)）在 NLP 领域掀起了范式的变革，特别是因为它引入了预训练/微调的范式：在大量文本数据上进行无监督的预训练后，模型可以在特定的下游任务上快速进行微调，且所需标签相对较少，因为它已经捕捉到了通用的语言模式……
