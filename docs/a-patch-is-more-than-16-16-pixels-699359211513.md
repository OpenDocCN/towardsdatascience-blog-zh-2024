# 一个 Patch 大小超过了 16*16 像素

> 原文：[https://towardsdatascience.com/a-patch-is-more-than-16-16-pixels-699359211513?source=collection_archive---------1-----------------------#2024-06-17](https://towardsdatascience.com/a-patch-is-more-than-16-16-pixels-699359211513?source=collection_archive---------1-----------------------#2024-06-17)

## 关于像素变换器和超长序列分布式变换器

[](https://mengliuz.medium.com/?source=post_page---byline--699359211513--------------------------------)[![Mengliu Zhao](../Images/0b950a0785fa065db3319ed5be4a91de.png)](https://mengliuz.medium.com/?source=post_page---byline--699359211513--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--699359211513--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--699359211513--------------------------------) [Mengliu Zhao](https://mengliuz.medium.com/?source=post_page---byline--699359211513--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--699359211513--------------------------------) ·阅读时间 7 分钟·2024年6月17日

--

你是否曾经想过，为什么Vision Transformer（ViT）使用16*16大小的patch作为输入token？

这一切可以追溯到变换器模型的早期。原始的变换器模型在2017年提出，且仅适用于自然语言数据。当BERT模型在2018年发布时，它只能处理最大长度为512的token序列。到了2020年，当GPT-3发布时，它能够处理长度为2048和4096的序列（3.5版本）。所有这些模型在处理序列到序列和文本生成任务时都展示了惊人的表现。

![](../Images/93f6207bcc1de07dfba8b303c8aa6e54.png)

图像来源：[https://pxhere.com/en/photo/141620](https://pxhere.com/en/photo/141620)

然而，当tokens按像素级别提取时，这些序列对于图像来说过于简短。例如，在Cifar-100中，图像大小是32*32 = 1024像素。在ImageNet中，图像大小是224*224 = 50176像素。如果直接将变换器应用于像素级别，序列长度将立即成为一个障碍。

ViT论文于2020年发布，提出使用patch而非像素作为输入token。对于一个224*224大小的图像，使用16*16的patch大小，序列长度将大幅减少至196，这完美解决了这个问题。

然而，这个问题仅部分解决了。**对于需要更精细特征的任务，必须采用不同的方法以恢复像素级的精度**。Segformer提出通过融合来自不同分辨率的层次化变换器编码器的特征来解决这个问题。SwinIR则结合了卷积神经网络（CNN）与变换器模块周围的多层跳跃连接进行细粒度特征提取。Swin Transformer是一个用于通用计算机视觉任务的模型，它从每个局部窗口的4*4补丁大小开始，然后逐渐构建到全球16*16补丁大小，以同时获得全局性和细粒度性。从本质上讲，这些努力指向一个事实——仅使用16*16大小的补丁是不够的。

自然的问题是，“像素”是否可以作为变换器的直接标记？这个问题进一步分为两个：1\. 是否可以将超长序列（例如，50k）输入变换器？2\. 将像素作为标记输入是否比输入补丁提供更多的信息？

在本文中，我将总结两篇最近的论文：1\. **像素变换器**，这是Meta AI上周发布的技术报告，从减少局部归纳偏置的角度，比较了像素级标记和补丁级标记在图像分类、预训练和生成等三项不同任务中的变换器模型表现。2\. **超长序列分布式变换器**：通过分布查询向量，作者展示了在3k个GPU上扩展50k长度输入序列的可能性。

**像素变换器（PiT）——从归纳偏置的角度**

Meta AI上周发布了技术报告，标题为：“**一张图比16*16的补丁更有价值**”。这份技术报告并没有提出一种新方法，而是回答了一个长期存在的问题：使用像素而不是补丁作为输入标记是否有意义？如果有，为什么？

论文从**局部归纳偏置**的角度进行分析。根据K. Murphy的著名[《机器学习书籍》](https://mitpress.mit.edu/9780262018029/machine-learning/)，**归纳偏置**是“*关于数据分布性质的假设*”。在早期的“非深度学习”时代，归纳偏置更多是“特征相关的”，源自为特定任务人工设计的特征。这个归纳偏置并不是坏事，尤其是在那些通过人类专家获得非常好先验知识的特定任务中，使得设计的特征非常有用。然而，从泛化的角度来看，设计的特征很难泛化到通用任务，比如通用的图像分类和分割。

除了特征偏差之外，架构本身也包含了归纳偏差。ViT 就是一个很好的例子，展示了在**架构层次结构、传播均匀性、表示规模和注意力局部性**方面，比 CNN 模型具有更少的归纳偏差。[详细讨论请参见我之前的 Medium 博客](https://mengliuz.medium.com/paper-reading-do-vision-transformers-see-like-convolutional-neural-networks-94d4fdd85ff3)。不过，ViT 仍然保留了一种特殊类型的归纳偏差——**局部性**。当 ViT 处理一系列 patch tokens 时，同一 patch 内的像素会自然地与来自不同 patch 的像素不同地被模型处理。这就是局部性的来源。

那么，是否有可能进一步去除局部性的归纳偏差？答案是肯定的。PiT 提出了使用“像素集”作为输入，并采用不同的位置编码（PE）策略：sin-cos、学习式和无编码。它在有监督、自监督和生成任务中展现了比 ViT 更优越的性能。下图展示了所提出的流水线。

![](../Images/7db566bffc4ae8c33bdb8c827f407ff7.png)

Pixel Transformer 流水线。图片来源：[https://arxiv.org/abs/2406.09415](https://arxiv.org/abs/2406.09415)

这个想法看似简单明了，作者声称他们“并没有引入一种新方法”。但是，PiT 仍展现出了巨大的潜力。在 CIFAR-100 和 ImageNet（输入大小缩小到 28*28）的有监督分类任务中，分类准确率比 ViT 提高了超过 2%。请参见下表。

![](../Images/097eeadf6fa6b09160fd8a8c18143bde.png)

有监督学习分类。图片来源：[https://arxiv.org/pdf/2406.09415](https://arxiv.org/pdf/2406.09415)

在自监督学习任务和图像生成任务中也观察到类似的提升。更重要的是，作者还展示了当将 patch 大小从 8*8 降到 1*1（单像素）时，性能提高的趋势，如下所示：

![](../Images/b221c83fb05588849fe8f07208d9dec5.png)

从 ViT（8*8 patch）到 PiT（1*1 patch）的性能提升。图片来自：[https://arxiv.org/pdf/2406.09415](https://arxiv.org/pdf/2406.09415)

**关于位置编码。**

正如[这篇研究论文](https://www.sciencedirect.com/science/article/abs/pii/S1047320322001845)中指出的那样，位置编码是基于 Transformer 的模型中，用于输入 token 序列排序和提高准确性的前提。然而，PiT 表明，即使去掉了位置编码，模型性能下降也很小：

![](../Images/f32198dc6792e2edbafdeaafb7df8a96.png)

使用三种不同位置编码（PE）的 PiT 性能：1. 固定的 sin-cos PE；2. 可学习的 PE；3. 无 PE。图片来源：[https://arxiv.org/pdf/2406.09415](https://arxiv.org/pdf/2406.09415)

为什么要丢弃位置编码？不仅仅是因为丢弃位置编码意味着很好地减少了局部性偏差。如果我们以分布式方式思考自注意力计算，它将大大减少跨设备通信的工作量，关于这一点我们将在下一节详细讨论。

**超长序列变换器——分布式查询向量解决方案**

**局部性归纳偏差只是讲了一部分故事**。如果仔细查看PiT论文中的结果，我们会发现实验限于28*28大小的调整图像，这是由于计算限制。但现实世界很少使用如此小尺寸的图像。所以，自然的问题是，尽管PiT可能有效并超越ViT，它能否在标准分辨率的自然图像上工作，例如244*244？

2023年发布的论文《超长序列分布式变换器》回答了这个问题。论文提出了一种解决方案，将50k长序列的变换器计算扩展到3k个GPU上。

![](../Images/70b5468468d4ea47bf4ca4644141f8d9.png)

原始变换器、基线并行化和所提的超长序列变换器。图片来源：[https://arxiv.org/pdf/2311.02382](https://arxiv.org/pdf/2311.02382)

这个想法很简单：变换器的瓶颈是自注意力计算。为了确保全局注意力计算，所提出的方法**只将查询向量**分配到不同设备上，同时在所有设备上保持相同的键值对副本。

![](../Images/472f0c435f3feb421a0b4ea20a4b965c.png)

自注意力计算分配的方程式。核心思想是分配 Q，并保持 K 和 V 的相同副本。图片来源：[https://arxiv.org/pdf/2311.02382](https://arxiv.org/pdf/2311.02382)

**位置编码感知的双重梯度平均**。对于具有可学习位置编码参数的架构，梯度反向传播与位置编码分布相关。因此，作者提出了双重梯度平均技术：当对同一序列的两个不同段执行梯度平均时，不涉及位置编码，但当需要对两个序列的对应段进行梯度平均时，位置编码参数会同步。

![](../Images/4dae73c9a02ec7f02e8dbde88032e6ee.png)

位置编码感知的双重梯度平均。图片来源：[https://arxiv.org/pdf/2311.02382](https://arxiv.org/pdf/2311.02382)

![](../Images/6c374168141f6f3ebf2daa014fd39012.png)

关于所使用GPU数量的最大序列长度。图片来源：[https://arxiv.org/pdf/2311.02382](https://arxiv.org/pdf/2311.02382)

当我们将这两篇论文结合起来时，事情变得有趣了。减少归纳偏差不仅有助于模型性能，还在分布式计算中起着至关重要的作用。

1.  **局部性和设备通信的归纳偏差**。PiT 论文展示了通过去除局部性偏差来构建无位置编码模型的潜力。此外，从分布式计算的角度来看，减少对 PE 的需求可以进一步减少跨设备的通信负担。

1.  **分布式序列计算中的局部性归纳偏差**。在多 GPU 上分布式自注意力意味着查询向量是基于段的。如果段依赖于连续的标记，则会有自然的局部性偏差。PiT 在“标记集”上进行计算，这意味着不需要连续的段，它将使查询向量没有偏差。

**参考文献：**

+   Nguyen 等人，《一张图片胜过 16x16 块：在单个像素上探索 Transformers》。arXiv 预印本 2024。

+   Wang 等人，《超长序列分布式 Transformer》。arXiv 预印本 2023。

+   Keles 等人，《自注意力的计算复杂度》。ALT 2023。

+   Jiang 等人，《视觉 Transformer 中位置嵌入的编码方法》。《视觉通信与图像表示杂志》。2022。

+   Xie 等人，《SegFormer：一种简洁高效的语义分割设计，基于 Transformers》。NeurIPS 2021。Github: [https://github.com/NVlabs/SegFormer](https://github.com/NVlabs/SegFormer)

+   Liang 等人，[Swinir：使用 Swin Transformer 进行图像恢复](https://openaccess.thecvf.com/content/ICCV2021W/AIM/html/Liang_SwinIR_Image_Restoration_Using_Swin_Transformer_ICCVW_2021_paper.html)。ICCV 2021。Github: [https://github.com/JingyunLiang/SwinIR](https://github.com/JingyunLiang/SwinIR)

+   Liu 等人，《Swin Transformer：使用平移窗口的分层视觉 Transformer》。ICCV 2021。Github: [https://github.com/microsoft/Swin-Transformer](https://github.com/microsoft/Swin-Transformer)

+   Dosovitsckiy 等人，《一张图像胜过 16x16 个词：用于大规模图像识别的 Transformers》。arXiv 预印本，2020。

+   Brown 等人，《语言模型是少量样本学习者》。NeurIPS 2020。Github: [https://github.com/openai/gpt-3](https://github.com/openai/gpt-3)

+   Devlin 等人，《BERT：用于语言理解的深度双向 Transformer 预训练》。*arXiv 预印本 2018*。HuggingFace 官方: [https://huggingface.co/docs/transformers/en/model_doc/bert](https://huggingface.co/docs/transformers/en/model_doc/bert)

+   Vaswani 等人，《Attention is all You need》。NeurIPS 2017。

+   Murphy，《机器学习：一种概率视角》。MIT 出版社 2012。
