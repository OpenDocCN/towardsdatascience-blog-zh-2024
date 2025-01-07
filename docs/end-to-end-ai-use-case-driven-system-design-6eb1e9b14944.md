# 端到端 AI 应用场景驱动的系统设计

> 原文：[`towardsdatascience.com/end-to-end-ai-use-case-driven-system-design-6eb1e9b14944?source=collection_archive---------10-----------------------#2024-03-07`](https://towardsdatascience.com/end-to-end-ai-use-case-driven-system-design-6eb1e9b14944?source=collection_archive---------10-----------------------#2024-03-07)

## 最佳性能/瓦特的技术全面列表

[](https://medium.com/@LizLiAI?source=post_page---byline--6eb1e9b14944--------------------------------)![Liz Li](https://medium.com/@LizLiAI?source=post_page---byline--6eb1e9b14944--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--6eb1e9b14944--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6eb1e9b14944--------------------------------) [Liz Li](https://medium.com/@LizLiAI?source=post_page---byline--6eb1e9b14944--------------------------------)

·发表于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--6eb1e9b14944--------------------------------) ·阅读时间 7 分钟·2024 年 3 月 7 日

--

定义 AI 性能最常用的指标是 TOPs（每秒万亿次操作），它表示计算能力，但简化了 AI 系统的复杂性。在真正的 AI 应用场景系统设计中，除了 TOPs 之外，还需要考虑许多其他因素，包括内存/缓存大小和带宽、数据类型、能效等。

此外，每个 AI 应用场景都有其独特性，需要对整个应用场景的流程进行全面审视。这种审视深入分析其对系统组件的影响，并探索优化技术，以预测最佳的流程性能。

![](img/f359afd76118bc72c1cfc8249e68a20a.png)

图片来自作者

在这篇文章中，我们选择了一个 AI 应用场景——一个端到端的实时无限缩放功能，使用稳定扩散-v2 修复模型，并研究如何构建一个具有最佳性能/瓦特的对应 AI 系统。这可以作为一个提案，包含了既有的成熟技术和新的研究思路，可能引发潜在的架构特性。

## 端到端视频缩放背景

+   如下图所示，为了缩小视频帧（鱼图像），我们先调整帧的大小并应用边框遮罩，然后将其输入稳定扩散修复管道。在输入文本提示的帮助下，该管道生成带有新内容的帧，以填补边框遮罩区域。这个过程不断应用于每一帧，以实现连续的缩放效果。为了节省计算资源，我们可以**稀疏采样视频帧，以避免对每一帧进行修复**（例如，每隔 5 帧生成 1 帧），如果这样仍能提供令人满意的用户体验。

![](img/296e2f107c1b9547cdab192a96052340.png)

帧生成。来源：[无限缩放稳定扩散 v2 和 OpenVINO™](https://docs.openvino.ai/2023.2/notebooks/236-stable-diffusion-v2-infinite-zoom-with-output.html) [1]

+   [稳定扩散-v2 修复](https://huggingface.co/stabilityai/stable-diffusion-2-inpainting)管道在稳定扩散-2 模型上进行预训练，该模型是由 Stability AI 和 LAION 创建的文本到图像潜在扩散模型。下图中的蓝色框显示了修复管道中的每个功能模块。

![](img/bc53101f16bb0e22374ffa92857fa6d7.png)

修复管道（输入包括文本提示、遮蔽图像和输入的随机噪声）。来源：[无限缩放稳定扩散 v2 和 OpenVINO™](https://docs.openvino.ai/2023.2/notebooks/236-stable-diffusion-v2-infinite-zoom-with-output.html) [1]

+   稳定扩散-2 模型生成 768*768 分辨率的图像，经过训练后，通过迭代去噪（50 步）从随机噪声中得到新的图像。去噪过程由 Unet 和调度器实现，这是一个非常缓慢的过程，需要大量的计算和内存。

![](img/91d84efdae6306a1826afd8fe5782b48.png)

稳定扩散-2 基础模型。来源：[插图版稳定扩散](https://jalammar.github.io/illustrated-stable-diffusion/) [2]

以下是管道中使用的 4 个模型：

1.  **VAE（图像编码器）**。将图像转换为低维潜在表示（64*64）。

1.  **CLIP（文本编码器）**。Transformer 架构（77*768），85MP。

1.  **UNet（扩散过程）**。通过调度算法进行迭代去噪处理，865M。

1.  **VAE（图像解码器）**。将潜在表示转换回图像（512*512）。

大多数稳定扩散操作（98%的自动编码器和文本编码器模型，以及 84%的 U-Net）是**卷积**。剩余的 U-Net 操作的大部分（16%）是密集矩阵乘法，原因在于自注意力模块。这些模型可能非常庞大（不同超参数会有所不同），因此需要大量内存。对于内存有限的移动设备来说，探索模型压缩技术以减少模型大小是至关重要的，包括量化（2-4 倍的模式大小缩减以及从 FP16 到 INT4 的 2-3 倍加速）、剪枝、稀疏性等。

## 针对 AI 特性（如端到端视频缩放）的电源效率优化。

对于像视频缩放这样的 AI 特性，电源效率是成功部署到边缘/移动设备上的关键因素之一。这些电池供电的边缘设备将能量储存在电池中，容量为 mW-H（毫瓦时，1200WH 意味着在一小时内消耗 1200 瓦的电能，如果应用程序每小时消耗 2 瓦，则电池可为设备提供 600 小时的电力）。电源效率的计算方法是 IPS/瓦特，其中 IPS 是每秒推理次数（对于基于图像的应用是 FPS/瓦特，TOPS/瓦特）。

减少功耗以延长移动设备的电池寿命至关重要，许多因素会导致高功耗，包括由于模型尺寸较大而导致的大量内存事务、矩阵乘法的高计算量等，让我们来看一下如何优化使用场景以实现高效的功耗管理。

1.  **模型优化。**

除了量化、剪枝和稀疏性之外，还有权重共享。网络中有许多冗余权重，而只有少数权重是有用的，可以通过让多个连接共享相同的权重来减少权重的数量，如下所示。原始的 4*4 权重矩阵被减少为 4 个共享权重和一个 2 位矩阵，总位数从 512 位减少到 160 位。

![](img/e68e1d591d59c9b468d5fc6e86a4ad8e.png)

权重共享。来源：[关于边缘人工智能（AI）优化技术的调查](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9919555/#B79-sensors-23-01279) [3]

2. **内存优化。**

内存是一个关键组件，相比矩阵乘法，它消耗更多的电力。例如，DRAM 操作的功耗可能是矩阵乘法操作的几个数量级。对于移动设备来说，将大模型适配到本地设备内存中往往是一个挑战。这导致了本地设备内存和 DRAM 之间大量的内存事务，从而带来更高的延迟和更大的能量消耗。

优化芯片外内存访问对提高能效至关重要。文章（[优化深度神经网络加速器的芯片外内存访问](https://ieeexplore.ieee.org/document/9708433) [4]）介绍了一种自适应调度算法，旨在最小化 DRAM 访问。该方法显著减少了能量消耗和延迟，减少幅度在 34%至 93%之间。

提出了一种新的方法（[ROMANet](https://arxiv.org/pdf/1902.10222.pdf) [5]），旨在通过减少内存访问来节省功耗。其核心思想是优化 CNN 层分区的正确块大小，以匹配 DRAM/SRAM 资源并最大化数据重用，同时还优化了数据块访问调度，以最小化 DRAM 访问次数。数据映射到 DRAM 时，重点是将数据块映射到同一行的不同列，以最大化行缓冲区命中率。对于更大的数据块，可以利用不同芯片中相同银行的并行性来实现芯片级并行性。此外，如果所有芯片的同一行已填满，则数据将映射到同一芯片中的不同银行，以实现银行级并行性。对于 SRAM，可以应用类似的银行级并行性概念。所提优化流程可以为 AlexNet 节省 12%的能量，为 VGG-16 节省 36%的能量，为 MobileNet 节省 46%的能量。下方展示了所提方法的高级流程图和 DRAM 数据映射的示意图。

![](img/b8499fe7fd9fbadd851d0ddf635f5ff4.png)

提出方法的操作流程。来源：[ROMANet](https://arxiv.org/pdf/1902.10222.pdf) [5]

![](img/5f26cf64625958c88fa93373ea35ffe1.png)

DRAM 数据在不同银行和芯片间的映射。来源：[ROMANet](https://arxiv.org/pdf/1902.10222.pdf) [5]

3. **动态功率调整。**

一个系统的功率可以通过 P=C*F*V²来计算，其中 F 是工作频率，V 是工作电压。像 DVFS（动态电压频率调整）这样的技术被开发用来优化运行时功耗。它根据工作负载的容量调整电压和频率。在深度学习中，逐层 DVFS 并不合适，因为电压调整有较长的延迟。另一方面，频率调整足够快，可以跟上每一层的要求。提出了一种针对 NPU 的[逐层动态频率调整（DFS）](https://onlinelibrary.wiley.com/doi/full/10.4218/etrij.2022-0094)[6]技术，使用功率模型预测功耗以确定最高允许频率。证明 DFS 可以提高 33%的延迟，节省 14%的能量。

![](img/cd452c14ae3934e8f58eb7d00d7fcfee.png)

8 个不同神经网络应用中，层级间频率变化。来源：[针对神经处理单元的逐层频率调整](https://onlinelibrary.wiley.com/doi/full/10.4218/etrij.2022-0094) [6]

4. **专用低功耗 AI 硬件加速器架构。** 为了加速深度学习推理，专门的 AI 加速器表现出卓越的功率效率，能够在降低功耗的同时实现类似的性能。例如，谷歌的 TPU 专门为加速矩阵乘法而设计，通过多次重用输入数据进行计算，与每次都需要获取数据的 CPU 不同。这种方法节省了功率，并减少了数据传输延迟。

## 结束

AI 推理仅仅是端到端用例流程的一部分，在优化系统功耗和性能时，还需要考虑其他子领域，包括图像处理、编解码、内存、显示、图形等。对过程进行细分并检查每个子领域的影响至关重要。例如，在运行无限缩放时，我们还需要考虑相机捕捉、视频处理系统、显示、内存等部分的功耗，确保每个组件的功耗预算得到优化。有许多优化方法，我们需要根据具体的用例和产品来确定优先级。

## 参考文献

[1] OpenVINO 教程：[无限缩放稳定扩散 v2 与 OpenVINO™](https://docs.openvino.ai/2023.2/notebooks/236-stable-diffusion-v2-infinite-zoom-with-output.html)

[2] [J](https://jalammar.github.io/)ay Alammar，[图解稳定扩散](https://jalammar.github.io/illustrated-stable-diffusion/)

[3] Chellammal Surianarayanan 等人，[边缘人工智能（AI）优化技术综述](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC9919555/#B79-sensors-23-01279)，2023 年 1 月

[4] Yong Zheng 等人，[优化深度神经网络加速器的片外内存访问](https://ieeexplore.ieee.org/document/9708433)，IEEE《电路与系统 II: 快捷简报》期刊，卷：69，期：4，2022 年 4 月

[5] Rachmad Vidya Wicaksana Putra 等人，[ROMANet: 基于细粒度重用驱动的片外内存访问管理与数据组织用于深度神经网络加速器](https://arxiv.org/pdf/1902.10222.pdf)，arxiv，2020 年

[6] Jaehoon Chung 等人，[神经处理单元的层次频率调节](https://onlinelibrary.wiley.com/doi/full/10.4218/etrij.2022-0094)，ETRI 期刊，卷：44，期：5，2022 年 9 月
