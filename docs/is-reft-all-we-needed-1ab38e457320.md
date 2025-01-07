# ReFT是我们所需要的一切吗？

> 原文：[https://towardsdatascience.com/is-reft-all-we-needed-1ab38e457320?source=collection_archive---------6-----------------------#2024-11-21](https://towardsdatascience.com/is-reft-all-we-needed-1ab38e457320?source=collection_archive---------6-----------------------#2024-11-21)

## 表示微调 — 超越PEFT技术的LLM微调

[](https://mengliuz.medium.com/?source=post_page---byline--1ab38e457320--------------------------------)[![Mengliu Zhao](../Images/0b950a0785fa065db3319ed5be4a91de.png)](https://mengliuz.medium.com/?source=post_page---byline--1ab38e457320--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1ab38e457320--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1ab38e457320--------------------------------) [孟柳·赵](https://mengliuz.medium.com/?source=post_page---byline--1ab38e457320--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1ab38e457320--------------------------------) ·阅读时间：6分钟·2024年11月21日

--

难道大家还没有开始使用ReFT吗？

斯坦福大学在2024年5月发布了论文[ReFT: Representation finetuning for language models](https://arxiv.org/abs/2404.03592)，立即展示了其巨大的潜力。2024年7月，[Oxen.ai展示了一项实验](https://www.oxen.ai/blog/fine-tuning-llama-3-in-14-minutes-using-reft)，在一台单独的Nvidia A10 GPU上，仅用了14分钟就完成了Llama3（8B）的微调，进一步证明了这一技术的强大。

与SOTA PEFT方法不同，后者专注于修改模型权重或输入，ReFT技术基于先前提出的[分布式互换干预（DII）](https://proceedings.mlr.press/v236/geiger24a.html)方法。DII方法首先将深度学习模型的嵌入投影到一个较低维度的子空间，然后通过该子空间进行干预，以实现微调目的。

在接下来的内容中，我们将首先带领读者了解SOTA微调PEFT算法，例如LoRA、提示微调和前缀微调；然后，我们将讨论原始的DII方法，为理解提供更好的背景；最后，我们将讨论ReFT技术，并展示论文中的实验结果。

![](../Images/4d8eb5635422e951d302e37a6821c5af.png)

图片来源：[https://pxhere.com/en/photo/1377005](https://pxhere.com/en/photo/1377005)

## PEFT — 参数高效微调技术

Hugging Face有一篇[博客详细介绍了不同的PEFT技术](https://huggingface.co/blog/peft)，用于LLM的微调。在这里，我们快速回顾一下这些技术。

**LoRA**于2021年提出，因其简单性和泛化能力，已成为微调LLMs和扩散模型（例如，[时变LoRA](https://openreview.net/forum?id=SgODU2mx9T)）最成功的技术之一。其理念非常简单：不是微调每一层的原始权重参数，而是LoRA技术添加了两个低秩矩阵，仅对低秩矩阵进行微调。在微调整个网络时，训练参数可以减少到不到0.3%，从而显著加快学习过程并减少GPU内存使用。

![](../Images/1a201f9802d82fb7b293865603fc973a.png)

LoRA模型更新。图片来源：[https://arxiv.org/pdf/2106.09685](https://arxiv.org/pdf/2106.09685)

**Prompt Tuning**技术的提出不再改变预训练模型的内部层，而是使用“*软提示*”，即可学习的任务特定提示嵌入作为前缀。给定混合任务批量提示，模型可以在不增加任务特定模型副本的情况下高效地执行多任务预测（与下图中的模型微调相比）。

![](../Images/eda3db8789900b021b199fbfcbfdfe3e.png)

提示微调与传统模型微调。图片来源：[https://arxiv.org/pdf/2104.08691](https://arxiv.org/pdf/2104.08691)

为了为大规模（例如，超过10B参数）的提示微调模型提供普适性，**Prefix Tuning (P-Tuning v2)**提出在不同层次前缀训练可学习的提示嵌入，从而允许在各个层级学习任务特定的信息。

![](../Images/5b57852181d21175d8ef208725dd0ec3.png)

P-tuning v2的多尺度提示。图片来源：[https://arxiv.org/pdf/2110.07602](https://arxiv.org/pdf/2110.07602)

在所有这些PEFT技术中，LoRA因其鲁棒性和高效性而最广泛应用于LLM的微调。详细的实证分析可以在这篇[论文](https://arxiv.org/pdf/2304.14999)中找到。

## 分布式干预互换（DII）

因果抽象是一种强大的人工智能框架，通过在因果模型（**高层**模型）和神经网络模型（或**低层**模型）之间进行干预，来诱导对齐估计。如果两个模型之间存在对齐关系，我们就知道因果模型和神经网络之间的潜在机制是相同的。通过干预发现潜在对齐的方法称为互换干预（II），这一概念在[讲座视频](https://www.youtube.com/watch?v=6pwpOOj33aw)中得到了直观的解释。

然而，经典的因果抽象方法使用暴力搜索通过所有可能的模型状态对齐，这种方法效率较低。**分布式交换干预（DII）**系统首先通过一系列**正交投影**将高阶和低阶模型投影到子空间，然后通过某些旋转操作生成一个干预后的模型。一个关于视觉模型的有趣干预实验可以在[这里](https://cs231n.stanford.edu/2024/papers/interchange-interventions-on-vision-models.pdf)找到。

更具体地，DII可以写成如下形式：

![](../Images/640b4e88d605cc53e71eb4bd800c6b66.png)

方程来源：[https://arxiv.org/pdf/2404.03592](https://arxiv.org/pdf/2404.03592)

其中，R是一个具有正交行的低秩矩阵，表示正交投影；**b**和**s**是模型从两个不同输入编码出的两种不同表示；干预将在低秩空间中发生，例如包含**Rs**和**Rb**的空间；投影矩阵**R**将通过**分布式对齐搜索（DAS）**进一步学习，该搜索旨在优化“[*干预后将最大化期望反事实输出概率的子空间*](https://arxiv.org/pdf/2404.03592)”。

## ReFT — 表示微调

因此，ReFT技术可以被视为在低维空间中对模型隐藏表示的干预，如下所示，其中\phi是干预，并直接应用于L层和P位置的隐藏表示：

![](../Images/3a64d382d5a269f8b54c65bdb43a7026.png)

在高层进行ReFT干预。图像来源：[https://arxiv.org/pdf/2404.03592](https://arxiv.org/pdf/2404.03592)

具体来说，本文进一步提出了**低秩线性子空间ReFT（LoReFT）**，并进一步引入了学习到的投影源：

![](../Images/4146ee3e4a8c3d618db11b3db0fb2c43.png)

方程来源：[https://arxiv.org/pdf/2404.03592](https://arxiv.org/pdf/2404.03592)

其中，**h**是隐藏表示，(**Rs = Wh + b**)是学习到的投影源，它在由**R**张成的低维空间中*编辑*表示h。现在，我们可以在下面的原始深度神经网络层中说明LoReFT。

![](../Images/7ada2c72f3bc9e51286b05b717c2c7cf.png)

图像来源：[https://arxiv.org/pdf/2404.03592](https://arxiv.org/pdf/2404.03592)

在**对大规模语言模型进行微调**时，语言模型的参数保持冻结，只有投影参数**\phi={R, W, b}**会被训练。

## **实验**

原始论文展示了将LoReFT（以及ReFT家族中的其他技术）与完整微调（FT）、LoRA、前缀调优等进行比较的实验，涵盖了四种基准测试：常识推理、算术推理、指令跟随和自然语言理解。我们可以看到，与LoRA相比，ReFT技术进一步减少了至少90%的参数，同时在性能上大幅超越。

![](../Images/2e85eede1c29e56a1025e6da9700dacd.png)

图片来源：[https://arxiv.org/pdf/2404.03592](https://arxiv.org/pdf/2404.03592)

## 讨论

为什么ReFT如此吸引人？首先，这项技术在Llama家族模型上提供了令人信服的结果，在各种基准测试中超越了当前最先进的微调方法（SOTA）。其次，这项技术深深植根于因果抽象算法中，提供了进一步的模型解释基础，尤其是从隐藏表示的角度来看。如原文所述，ReFT表明“*一个分布在一组神经元上的线性子空间可以实现对大量任务的泛化控制*”，这可能为我们更好地理解大型语言模型打开新的大门。

## 参考文献

+   Wu Z, Arora A, Wang Z, Geiger A, Jurafsky D, Manning CD, Potts C. ReFT：语言模型的表示微调。arXiv预印本 arXiv:2404.03592。2024年4月4日。

+   Hu EJ, Shen Y, Wallis P, Allen-Zhu Z, Li Y, Wang S, Wang L, Chen W. LoRA：大规模语言模型的低秩适配。arXiv预印本 arXiv:2106.09685。2021年6月17日。

+   Zhuang Z, Zhang Y, Wang X, Lu J, Wei Y, Zhang Y. 时变LoRA：面向扩散模型的跨领域微调。2024年神经信息处理系统年会（The Thirty-eighth Annual Conference on Neural Information Processing Systems）。

+   Liu X, Ji K, Fu Y, Tam WL, Du Z, Yang Z, Tang J. P-tuning v2：提示调优在各个规模和任务中的普适性与微调相当。arXiv预印本 arXiv:2110.07602。2021年10月14日。

+   Geiger A, Wu Z, Potts C, Icard T, Goodman N. 在《因果学习与推理》2024年3月15日（第160–187页）中找到可解释的因果变量与分布式神经表示之间的对齐。PMLR。

+   Lester B, Al-Rfou R, Constant N. 参数高效提示调优的规模效应。arXiv预印本 arXiv:2104.08691。2021年4月18日。

+   Pu G, Jain A, Yin J, Kaplan R. 对LLM的PEFT技术优缺点的实证分析。arXiv预印本 arXiv:2304.14999。2023年4月28日。
