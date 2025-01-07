# PyTorch 自动混合精度库背后的谜团

> 原文：[https://towardsdatascience.com/the-mystery-behind-the-pytorch-automatic-mixed-precision-library-d9386e4b787e?source=collection_archive---------4-----------------------#2024-09-17](https://towardsdatascience.com/the-mystery-behind-the-pytorch-automatic-mixed-precision-library-d9386e4b787e?source=collection_archive---------4-----------------------#2024-09-17)

## 如何通过三行代码实现2倍速度提升的模型训练

[](https://mengliuz.medium.com/?source=post_page---byline--d9386e4b787e--------------------------------)[![Mengliu Zhao](../Images/0b950a0785fa065db3319ed5be4a91de.png)](https://mengliuz.medium.com/?source=post_page---byline--d9386e4b787e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d9386e4b787e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d9386e4b787e--------------------------------) [孟流赵](https://mengliuz.medium.com/?source=post_page---byline--d9386e4b787e--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d9386e4b787e--------------------------------) ·阅读时长8分钟·2024年9月17日

--

你是否曾希望你的深度学习模型能够更快运行？

GPU昂贵，数据集庞大，训练过程似乎没有尽头；你有一百万个实验要运行，还有一个截止日期要赶——所有这些都是你期望某种形式的训练加速的充分理由。

但该选择哪个呢？

已经有许多关于模型训练性能调优的优秀参考资料，来自于[PyTorch](https://pytorch.org/tutorials/recipes/recipes/tuning_guide.html)、[HuggingFace](https://huggingface.co/docs/transformers/v4.15.0/performance)和[Nvidia](https://docs.nvidia.com/deeplearning/performance/index.html)，包括异步数据加载、缓冲区检查点、分布式数据并行和**自动混合精度**。

在这篇文章中，我将介绍自动混合精度技术。我将从简要介绍Nvidia的张量核心设计开始，然后介绍2018年在ICLR上发表的开创性论文《混合精度训练》，最后通过一个简单的示例展示如何在FashionMNIST上训练ResNet50，并通过加载2倍批量大小实现2倍的训练速度提升，同时只需**增加三行代码**。

![](../Images/c4fce0287d310bd6839f85ae33144b2a.png)

图片来源：[https://pxhere.com/en/photo/872846](https://pxhere.com/en/photo/872846)

## **硬件基础 — Nvidia 张量核心**

首先，让我们回顾一下GPU设计的一些基本知识。Nvidia GPU中最受欢迎的商业产品之一是Volta系列，例如V100 GPU，基于GV100 GPU设计。因此，我们将*基于GV100架构*展开讨论。

对于 GV100，**流多处理器（SM）**是计算的核心设计。每个 GPU 包含 6 个 GPU 处理集群（GPC）和 84 个 SM（V100 为 80 个 SM）。整体设计如下图所示。

![](../Images/dbf7020857a40f17d2576b5ced5b5123.png)

Volta GV100 GPU 设计。每个 GPU 包含 6 个 GPC（图形处理集群），每个 GPC 包含 14 个 SM（流多处理器）。图像来源：[https://arxiv.org/pdf/1803.04014](https://arxiv.org/pdf/1803.04014)

对于每个 SM，它包含两种类型的核心：CUDA 核心和 Tensor 核心。**CUDA 核心**是 Nvidia 在 2006 年推出的原始设计，是 CUDA 平台的核心部分。CUDA 核心可以分为三种类型：FP64 核心/单元、FP32 核心/单元和 Int32 核心/单元。每个 GV100 SM 包含 32 个 FP64 核心，64 个 FP32 核心和 64 个 Int32 核心。**Tensor 核心**是在 Volta/Turing（2017）系列 GPU 中引入的，与之前的 Pascal（2016）系列 GPU 区别开来。每个 GV100 SM 包含 8 个 Tensor 核心。V100 GPU 的详细规格请参见[这里](https://datacrunch.io/blog/nvidia-v100-gpu-specs)。下面是 SM 设计的详细图示。

![](../Images/e200f32cb2b5c8c316fddbb749d81e98.png)

流处理器（SM）的子波段。每个 SM 包含四个子波段。图像来源：[https://arxiv.org/pdf/1903.03640](https://arxiv.org/pdf/1903.03640)

**为什么是 Tensor 核心？Nvidia 的 Tensor 核心专门用于执行通用矩阵乘法（GEMM）和半精度矩阵乘法与累加（HMMA）操作。** 简而言之，GEMM 执行 A*B + C 的矩阵运算，而 HMMA 将操作转换为半精度格式。详细讨论请参见[这里](https://leimao.github.io/blog/NVIDIA-Tensor-Core-Programming/)。由于深度学习在训练过程中大量涉及 MMA，**Tensor 核心在当今的模型训练和加速中至关重要**。

![](../Images/79b6bd3fc6116e9b9ae38ea680529295.png)

GEMM 操作示例。对于 HMMA，A 和 B 通常转换为 FP16，而 C 和 D 可以是 FP16 或 FP32。图像来源：[https://arxiv.org/pdf/1811.08309](https://arxiv.org/pdf/1811.08309)

当然，在切换到混合精度训练时，始终检查您使用的 GPU 的规格。只有[最新的 GPU 系列支持 Tensor 核心](https://www.wevolver.com/article/tensor-cores-vs-cuda-cores)，并且混合精度训练仅能在这些机器上使用。

## **数据格式基础 — 单精度（FP32）与半精度（FP16）**

现在，让我们更仔细地了解 FP32 和 FP16 格式。FP32 和 FP16 是 IEEE 格式，分别使用 32 位和 16 位二进制存储表示浮动数值。这两种格式都包含三个部分：a）符号位，b）指数位，c）尾数位。FP32 和 FP16 的区别在于**分配给指数和尾数的位数**，这导致了不同的值范围和精度。

![](../Images/215ad72e4c2afa8517c0686351fff244.png)

FP16（IEEE标准）、BF16（Google Brain标准）、FP32（IEEE标准）和TF32（Nvidia标准）之间的差异。图片来源：[https://en.wikipedia.org/wiki/Bfloat16_floating-point_format](https://en.wikipedia.org/wiki/Bfloat16_floating-point_format)

如何将FP16和FP32转换为实际值？根据IEEE-754标准，FP32的十进制值 = (-1)^(符号位) × 2^(十进制指数 —127) ×（隐含的前导1 + 十进制尾数），其中127是偏置指数值。对于FP16，公式变为(-1)^(符号位) × 2^(十进制指数 — 15) ×（隐含的前导1 + 十进制尾数），其中15是相应的偏置指数值。更多关于偏置指数值的详细信息，请参见[此处](https://en.wikipedia.org/wiki/Exponent_bias)。

在这个意义上，FP32的数值范围大约是[-2¹²⁷, 2¹²⁷] ~[-1.7*1e38, 1.7*1e38]，而FP16的数值范围大约是[-2¹⁵, 2¹⁵]=[-32768, 32768]。注意，FP32的十进制指数范围在0到255之间，我们排除了最大值0xFF，因为它表示NAN。因此，最大的十进制指数是254–127 = 127。FP16也适用类似的规则。

对于精度，注意到指数和尾数都对精度限制有贡献（这也叫做**非标准化**，参见[详细讨论](https://cs.stackexchange.com/questions/101632/understanding-denormalized-numbers-in-floating-point-representation)），因此FP32可以表示精度达到2^(-23)*2^(-126)=2^(-149)，而FP16的精度可以达到2^(10)*2^(-14)=2^(-24)。

FP32和FP16表示的差异带来了混合精度训练的关键问题，因为**深度学习模型的不同层/操作对数值范围和精度的敏感度不同，需要分别处理**。

## **混合精度训练**

现在我们已经了解了MMA的硬件基础、Tensor核心的概念以及FP32与FP16之间的关键区别，接下来可以进一步讨论混合精度训练的细节。

混合精度训练的概念最早出现在2018年ICLR论文“[混合精度训练](https://arxiv.org/abs/1710.03740)”中，该论文在训练过程中将深度学习模型转换为半精度浮点数，而不会损失模型精度或修改超参数。如前所述，由于FP32和FP16之间的主要区别是数值范围和精度，论文详细讨论了**FP16如何导致梯度消失**以及如何通过**损失缩放**解决该问题。此外，论文还提出了使用FP32主权重副本和在特定操作（如归约和向量点积累加）中使用FP32等技巧。

**损失缩放**。论文中给出了使用FP32精度训练Multibox SSD检测器网络的示例，如下所示。如果不进行任何缩放，FP16梯度的指数范围将≥2^(-24)，低于此值的部分将变为零，这相较于FP32来说是不够的。然而，通过实验发现，只需将梯度缩放2³=8倍，即可将半精度训练的准确度恢复至与FP32相匹配。从这个角度看，作者认为[2^(-27), 2^(-24)]之间的少数梯度仍然在训练过程中很重要，而低于2^(-27)的值则不重要。

![](../Images/7a45fe75b18cd7c86e9d63a193f9f30d.png)

在Multibox SSD训练示例中，使用FP32精度时梯度值的范围。请注意，值范围在[2^(-27), 2^(-24)]之间超出了FP16的非正规化范围，仅占总梯度的几百分比，但在整体训练中仍然很重要。图片来源：[https://arxiv.org/pdf/1710.03740](https://arxiv.org/pdf/1710.03740)

解决这一缩放差异的方法是应用损失缩放。根据链式法则，缩放损失将确保所有梯度都按相同的比例进行缩放。在最终的权重更新之前，需要将梯度恢复为原始尺度。

## **自动混合精度训练**

Nvidia最早开发了自动混合精度训练，作为一个名为APEX的PyTorch扩展，后来被PyTorch、TensorFlow、MXNet等主流框架广泛采用。有关详细信息，请参阅Nvidia的[文档](https://docs.nvidia.com/deeplearning/performance/mixed-precision-training/index.html#)。为简便起见，我们将仅介绍PyTorch的自动混合精度库：[https://pytorch.org/docs/stable/amp.html](https://pytorch.org/docs/stable/amp.html)。

amp库可以自动处理大多数混合精度训练技术，例如FP32主权重复制。用户主要接触到**操作自动转换**和**梯度/损失缩放**。

**操作自动转换**。尽管我们提到过张量核心可以大幅提升GEMM操作的性能，但某些操作并不适合使用半精度表示。

amp库提供了一个[CUDA操作列表](https://pytorch.org/docs/stable/amp.html#autocast-op-reference)，列出了可用于半精度的操作。大多数矩阵乘法、卷积和线性激活都可以通过amp.autocast完全覆盖，但是对于归约/求和、softmax和损失计算，这些计算仍然在FP32精度下进行，因为它们对数据范围和精度更为敏感。

**梯度/损失缩放**。amp库提供了[自动梯度缩放](https://docs.nvidia.com/deeplearning/performance/mixed-precision-training/index.html#scalefactor)技术，因此用户在训练过程中无需手动调整缩放。有关缩放因子的更详细算法可以在[这里](https://docs.nvidia.com/deeplearning/performance/mixed-precision-training/index.html#scalefactor)找到。

一旦梯度被缩放，必须在梯度裁剪和正则化之前进行缩放回原值。更多细节可以在[这里](https://pytorch.org/docs/stable/amp.html#autocast-op-reference)找到。

## **一个 FashionMNIST 训练示例**

torch.amp 库相对容易使用，只需要三行代码就能将训练速度提升 2 倍。

我们从一个非常简单的任务开始，在 FashionMNIST 数据集上训练 ResNet50 模型（[MIT 许可证](https://github.com/zalandoresearch/fashion-mnist/blob/master/LICENSE)），使用 FP32；我们可以看到，十个 Epoch 的训练时间为 333 秒：

![](../Images/aa7d6413a871ff3c3b806bff458fcddd.png)

ResNet50 在 FashionMNIST 上的训练。图像由作者提供。

![](../Images/35c410a68cae78f6b83badcb647fa49e.png)

小于 2**(-24) 的梯度与总梯度的比值。我们可以看到，FP16 会将几乎四分之一的总梯度归零。图像由作者提供。

![](../Images/aa65d416e3eb94c05cc56ef30eab8e1e.png)

评估结果。图像由作者提供。

现在我们使用了 amp 库。amp 库只需要额外三行代码来进行混合精度训练。我们可以看到训练在 141 秒内完成，比 FP32 训练速度提升了 2.36 倍，同时精度、召回率和 F1-score 保持不变。

```py
scaler = torch.cuda.amp.GradScaler()
# start your training code
# ...
with torch.autocast(device_type="cuda"):
  # training code

# wrapping loss and optimizer
scaler.scale(loss).backward()
scaler.step(optimizer)

scaler.update()
```

![](../Images/130adb13d32d608790dbc8b828d9ea5f.png)

使用 amp 的训练代码。图像由作者提供。

![](../Images/d6afd6d8ec261110cf753fb45cfdbea1.png)

训练中的缩放因子。缩放因子仅在第一步发生变化，并保持不变。图像由作者提供。

![](../Images/60a8df649cc72aa64cdad00e012f89f2.png)

最终结果与 FP32 训练结果可比。图像由作者提供。

上面的代码的 GitHub 链接在[这里](https://github.com/adoskk/MachineLearningBasics/blob/main/mixed_precision_training/mixed_precision_training.ipynb)。

## 总结

混合精度训练是加速深度学习模型训练的一个宝贵技术。它不仅加速了浮点运算，还节省了 GPU 内存，因为训练批次可以转换为 FP16，从而节省了一半的 GPU 内存。通过 PyTorch 的 amp 库，额外的代码可以减少为三行，因为权重复制、损失缩放、操作类型转换等都由库内部处理。

然而，混合精度训练并没有真正解决 GPU 内存问题，特别是在模型权重大小远大于数据批量时。一方面，只有模型的某些层被转换为 FP16，其余部分仍以 FP32 计算；其次，权重更新仍然需要 FP32 副本，这仍然占用大量 GPU 内存；第三，像 Adam 这样的优化器的参数在训练过程中会占用大量 GPU 内存，而混合精度训练保持优化器参数不变。从这个角度来看，更先进的技术，如 DeepSpeed 的 ZERO 算法，是必要的。

## **参考文献**

+   Micikevicius 等人，《混合精度训练》。ICLR 2018

+   PyTorch AMP 库: [https://pytorch.org/tutorials/recipes/recipes/amp_recipe.html](https://pytorch.org/tutorials/recipes/recipes/amp_recipe.html)

+   Nvidia CUDA 浮点运算: [https://docs.nvidia.com/cuda/floating-point/index.html](https://docs.nvidia.com/cuda/floating-point/index.html)
