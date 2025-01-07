# 使用稳定扩散 3 生成图像的不同方式

> 原文：[`towardsdatascience.com/different-ways-of-image-generation-with-stable-diffusion-3-763b9d34d120?source=collection_archive---------14-----------------------#2024-06-28`](https://towardsdatascience.com/different-ways-of-image-generation-with-stable-diffusion-3-763b9d34d120?source=collection_archive---------14-----------------------#2024-06-28)

## 在 Google Colab 和本地 PC 上运行 SD3

[](https://dmitryelj.medium.com/?source=post_page---byline--763b9d34d120--------------------------------)![Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--763b9d34d120--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--763b9d34d120--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--763b9d34d120--------------------------------) [Dmitrii Eliuseev](https://dmitryelj.medium.com/?source=post_page---byline--763b9d34d120--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--763b9d34d120--------------------------------) ·阅读时间 7 分钟·2024 年 6 月 28 日

--

![](img/c0337b04a58c0bf60b325e59c772c40c.png)

图像由作者生成（SD3，付费创作者许可）

稳定扩散被认为是最好的开源文本生成图像模型之一，令人兴奋的是，最新的“Stable Diffusion 3 Medium”模型于 2024 年 6 月发布，并且现在可以在[HuggingFace](https://huggingface.co/stabilityai/stable-diffusion-3-medium)上获取。该模型在非商业研究社区许可下免费提供，因此让我们编写一些 Python 代码，看看它是如何工作的！

## 一般信息

稳定扩散（SD）是一个文本生成图像的模型；这种方法本身相对较新。关于使用潜在扩散模型进行图像合成的第一篇论文（Rombach 等人的“[高分辨率图像合成与潜在扩散模型](https://arxiv.org/pdf/2112.10752)”）于 2022 年发布。一家名为 Stability AI 的公司对该项目产生了兴趣，随后该模型在 2022 年 8 月发布，命名为“Stable Diffusion”。之后，它经过了几次改进。在撰写本文时，Stable Diffusion 1.1 仍然可以在[HuggingFace](https://huggingface.co/CompVis/stable-diffusion-v1-1)上使用，[最新版本](https://huggingface.co/stabilityai/stable-diffusion-3-medium) 3 于 2024 年 6 月发布。

现在，让我们在 Python 中运行该模型。

## 基本测试

使用 HuggingFace 🤗 [Diffusers](https://github.com/huggingface/diffusers) 库运行稳定扩散非常简单：

```py
import torch
from diffusers…
```
