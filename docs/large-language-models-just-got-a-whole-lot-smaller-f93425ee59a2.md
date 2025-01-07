# 大型语言模型变得更小了

> 原文：[https://towardsdatascience.com/large-language-models-just-got-a-whole-lot-smaller-f93425ee59a2?source=collection_archive---------1-----------------------#2024-03-20](https://towardsdatascience.com/large-language-models-just-got-a-whole-lot-smaller-f93425ee59a2?source=collection_archive---------1-----------------------#2024-03-20)

## 这可能如何改变软件初创公司的竞争格局

[](https://arijoury.medium.com/?source=post_page---byline--f93425ee59a2--------------------------------)[![Ari Joury, PhD](../Images/5b9e49279fb3f26373b393f29a4daaf7.png)](https://arijoury.medium.com/?source=post_page---byline--f93425ee59a2--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--f93425ee59a2--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--f93425ee59a2--------------------------------) [Ari Joury, PhD](https://arijoury.medium.com/?source=post_page---byline--f93425ee59a2--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--f93425ee59a2--------------------------------) ·15分钟阅读·2024年3月20日

--

![](../Images/afd673415e30151b37e539a583a1e1f5.png)

LLM正在变得越来越小、越来越高效！图片灵感来自 [Benjamin Marie](/run-llama-2-70b-on-your-gpu-with-exllamav2-588141a88598)

**本文由** [**David Meiborg**](https://medium.com/u/8523370997f4?source=post_page---user_mention--f93425ee59a2--------------------------------)**共同撰写。**

*TLDR：大型语言模型（简称LLMs）目前非常庞大，运行成本高，而且具有* [*显著的碳足迹*](https://arxiv.org/abs/2309.14393)*。然而，近期在模型压缩和系统级优化方法上的进展可能会增强LLM的推理能力。特别是，使用具有三元结构的参数的方法，有可能绕过当前标准的成本高昂的矩阵乘法。这对那些生产专用芯片的硬件初创公司以及那些使用或定制自己LLM的软体初创公司来说，带来了激动人心的前景。那些帮助客户部署LLM的初创公司也可能会迎来更多的商业机会。*

现在的大型语言模型非常庞大。可以说，真的很大。如果你想加载一个LlaMa-2–70B模型，你需要140 GB的显存（这相当于70亿个参数，每个参数占用2字节）。相比之下，像NVIDIA RTX 3090或4090这样的GPU只有24 GB的显存——这只是所需显存的一小部分。

有一些[量化的变通方法](/run-llama-2-70b-on-your-gpu-with-exllamav2-588141a88598)，但这些方法通常很麻烦。你很可能仍然需要让你的GPU工作长达15小时，直到模型加载完成。更不用说你仍然...
