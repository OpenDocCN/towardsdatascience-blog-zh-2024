# 从视觉变换器到掩码自编码器，5分钟搞定

> 原文：[https://towardsdatascience.com/from-vision-transformers-to-masked-autoencoders-in-5-minutes-cfd2fa1664ac?source=collection_archive---------3-----------------------#2024-06-28](https://towardsdatascience.com/from-vision-transformers-to-masked-autoencoders-in-5-minutes-cfd2fa1664ac?source=collection_archive---------3-----------------------#2024-06-28)

## 自然语言处理任务如何泛化到计算机视觉的简单指南

[](https://essamwissam.medium.com/?source=post_page---byline--cfd2fa1664ac--------------------------------)[![Essam Wisam](../Images/6320ce88ba2e5d56d70ce3e0f97ceb1d.png)](https://essamwissam.medium.com/?source=post_page---byline--cfd2fa1664ac--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--cfd2fa1664ac--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--cfd2fa1664ac--------------------------------) [Essam Wisam](https://essamwissam.medium.com/?source=post_page---byline--cfd2fa1664ac--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--cfd2fa1664ac--------------------------------) ·阅读时长7分钟·2024年6月28日

--

几乎所有的自然语言处理任务，从语言建模、掩码词预测到翻译和问答，都在2017年变换器架构首次亮相时经历了革命性变化。毫不奇怪，在短短2至3年内，变换器也被应用到计算机视觉任务中，并且在这些任务中展现了卓越的表现。在这篇文章中，我们探讨了两种基础架构，使得变换器能够突破进入计算机视觉领域。

## 目录

· [视觉变换器](#c206)

∘ [关键思想](#c302)

∘ [操作](#98e9)

∘ [混合架构](#ae5c)

∘ [结构的丧失](#af38)

∘ [结果](#6ed9)

∘ [自监督学习通过掩码](#7607)

· [掩码自编码器视觉变换器](#e126)

∘ [关键思想](#2db8)

∘ [架构](#3393)

∘ [最终备注与示例](#f78c)

# 视觉变换器

![](../Images/f02bd048ad2f7f140d55716a7d214624.png)

论文中的图片：“一张图片值16x16个词：用于大规模图像识别的变换器”

## 关键思想

视觉变换器的目的是将[标准变换器](https://medium.com/@essamwissam/a-systematic-explanation-of-transformers-db82e039b913)架构推广到处理和学习图像输入。关于该架构有一个关键思想，作者在文中非常透明地进行了强调：

> “受变换器在自然语言处理领域扩展成功的启发，我们尝试将标准变换器直接应用于图像，尽可能少地做修改。”

## 操作

将“*尽可能少的修改*”字面理解是合理的，因为他们几乎没有做任何修改。实际上他们修改的是输入结构：

+   在NLP中，transformer编码器接收一个***one-hot向量序列***（或者等价的令牌索引），这些向量***代表输入句子/段落***，并返回一个上下文嵌入向量序列，后者可以用于进一步的任务（例如分类）。

+   为了推广CV，视觉transformer接收一个***patch向量序列***，这些向量代表了***输入图像***，并返回一个上下文嵌入向量序列，后者可以用于进一步的任务（例如分类）。

特别地，假设输入图像的维度是(n,n,3)，并将其作为输入传递给transformer，视觉transformer所做的是：

+   它将图像分成k²个patch，其中k是某个值（例如，k=3），如上图所示。

+   现在每个patch的维度将是(n/k,n/k,3)，下一步是将每个patch展平为一个向量。

patch向量的维度将是3*(n/k)*(n/k)。例如，如果图像的维度是(900,900,3)，并且我们使用k=3，那么一个patch向量的维度将是300*300*3，表示展平后的patch中的像素值。在论文中，作者使用了k=16。因此，论文的标题是“An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale”，而不是输入一个表示单词的one-hot向量，它们代表的是表示图像patch的像素向量。

**其余操作与原始transformer编码器相同：**

+   这些patch向量会经过一个可训练的嵌入层。

+   在每个向量中添加位置嵌入，以保持图像中的空间信息。

+   输出是*num_patches*个编码表示（每个patch一个），这些表示可以用于对patch或图像层面的分类。

+   更常见的是（如论文中所示），一个CLS标记被加到表示序列的最前面，这个标记用于对整个图像进行预测（类似于BERT）。

**那transformer解码器怎么样？**

记住，这就像transformer编码器一样；不同之处在于，它使用了掩码自注意力而不是自注意力（但输入签名保持不变）。无论如何，你应该预期很少使用仅解码器的transformer架构，因为简单地预测下一个patch可能不是一个很感兴趣的任务。

## 混合架构

作者还提到，完全可以从CNN特征图开始，而不是直接从图像开始，形成一种混合架构（CNN将输出馈送给视觉transformer）。在这种情况下，我们可以把输入看作是一个通用的(n,n,p)特征图，而一个patch向量的维度将是(n/k)*(n/k)*p。

## 结构丢失

你可能会想，这种架构不应该那么好，因为它把图像当作线性结构来处理，但实际上并非如此。作者通过提到这一点，试图表明这是故意为之。

> “二维邻域结构的使用非常有限……在初始化时，位置嵌入不包含任何关于补丁的二维位置信息，所有补丁之间的空间关系必须从头开始学习。”

我们将看到，变换器（transformer）能够学习这一点，这一点在他们的实验中得到了验证，更重要的是，下一篇论文中的架构也证明了这一点。

## 结果

结果的主要结论是，对于小数据集，视觉变换器通常不如基于CNN的模型，但对于较大数据集，它们能够接近或超越CNN模型，并且无论如何都需要显著更少的计算资源：

![](../Images/7e699867cbb8ab95893959a4509a990f.png)

来自论文的表格：“一张图片价值16x16个词：用于大规模图像识别的变换器”。

在这里，我们看到对于JFT-300M数据集（包含3亿张图片），在该数据集上预训练的ViT模型表现超过了基于ResNet的基准模型，同时预训练所需的计算资源明显更少。如图所示，他们使用的最大视觉变换器（ViT-Huge，拥有6.32亿参数，k=16）所需的计算量仅为ResNet基准模型的25%，且仍然优于其性能。即使是ViT-Large，仅使用不到6.8%的计算资源，其性能也几乎没有下降。

同时，其他研究也展示了ResNet在训练时在ImageNet-1K（仅包含130万张图片）上的表现显著更好。

## 通过遮罩进行的自监督学习

作者们对遮罩补丁预测进行了初步探索，模仿了BERT中使用的遮罩语言模型任务（即，遮罩出一些补丁并尝试预测它们）。

> “我们使用遮罩补丁预测目标进行初步的自监督实验。为此，我们将50%的补丁嵌入破坏，通过以下方式之一：将其嵌入替换为可学习的[遮罩]嵌入（80%）、随机替换为其他补丁嵌入（10%），或者保持原样（10%）。”

通过自监督预训练，他们的小型ViT-Base/16模型在ImageNet上的准确率达到了79.9%，相比从零开始训练提升了2%，但仍然比监督预训练低4%。

# 遮罩自编码器视觉变换器

![](../Images/d7cf73cf4ebc0d818ec829d32a024a11.png)

来自论文的图片：《Masked Autoencoders Are Scalable Vision Learners》

## 关键思想

正如我们从视觉变换器的论文中看到的，通过遮罩输入图像的补丁进行预训练的收益并不像在普通NLP任务中那样显著，在NLP中，遮罩预训练可以在某些微调任务中带来最先进的结果。

本文提出了一种视觉变换器架构，涉及一个编码器和解码器，当通过遮罩进行预训练时，能显著提高基准视觉变换器模型的表现（与使用监督训练的基准模型相比，提升高达6%）。

![](../Images/027d64201111d63dce657b96d4ad9c3c.png)

来自论文的图像：*Masked Autoencoders Are Scalable Vision Learners*

这是一些示例（输入、输出、真实标签）。它是一个自编码器，旨在重建输入的同时填充缺失的补丁。

## 架构

他们的**编码器**实际上是我们之前解释的普通视觉变换器编码器。在训练和推理过程中，它只处理“观察到的”补丁。

与此同时，他们的**解码器**实际上是普通的视觉变换器编码器，但它需要：

+   缺失补丁的遮蔽标记向量

+   已知补丁的编码器输出向量

所以对于一个图像 [ [ A, B, X], [C, X, X], [X, D, E]]，其中X表示缺失的补丁，解码器将处理补丁向量的序列 [Enc(A), Enc(B), Vec(X), Vec(X), Vec(X), Enc(D), Enc(E)]。Enc返回给定补丁向量的编码器输出向量，X是表示缺失标记的向量。

**解码器中的最后一层**是一个线性层，它将上下文嵌入（由解码器中的视觉变换器编码器生成）映射到一个长度等于补丁大小的向量。损失函数是均方误差，它计算原始补丁向量与此层预测补丁向量之间的差值平方。在损失函数中，我们只关注解码器对遮蔽标记的预测，忽略与已存在的标记（即Dec(A)、Dec(B)、Dec(C)等）对应的部分。

## 最后的评论和示例

可能令人惊讶的是，作者建议遮蔽图像中约75%的补丁，而BERT仅会遮蔽大约15%的单词。他们是这样辩解的：

> 图像是具有大量空间冗余的自然信号——例如，缺失的补丁可以通过相邻的补丁恢复，而无需高层次的理解部分、物体和场景。为了克服这一差异并鼓励学习有用的特征，我们会遮蔽大量的随机补丁。

想自己试试吗？查看NielsRogge提供的[演示笔记本](https://colab.research.google.com/github/NielsRogge/Transformers-Tutorials/blob/master/ViTMAE/ViT_MAE_visualization_demo.ipynb)。

这就是这个故事的全部内容。我们经历了一段旅程，理解了基本的变换器模型如何推广到计算机视觉领域。希望你觉得它清晰、有洞察力，且值得你的时间。

参考文献：

[1] Dosovitskiy, A. *et al.* (2021) *An image is worth 16x16 words: Transformers for image recognition at scale*, *arXiv.org*。可在以下网址访问：[https://arxiv.org/abs/2010.11929](https://arxiv.org/abs/2010.11929)（访问时间：2024年6月28日）。

[2] He, K. *et al.* (2021) *Masked autoencoders are scalable vision learners*, *arXiv.org*。可在以下网址访问：[https://arxiv.org/abs/2111.06377](https://arxiv.org/abs/2111.06377)（访问时间：2024年6月28日）。
