# CLIP、LLaVA 和大脑

> 原文：[https://towardsdatascience.com/clip-llava-and-the-brain-2073dfb33d7e?source=collection_archive---------3-----------------------#2024-06-19](https://towardsdatascience.com/clip-llava-and-the-brain-2073dfb33d7e?source=collection_archive---------3-----------------------#2024-06-19)

## 深度学习与大脑

## 来自神经科学的多模态变换器洞察

[](https://medium.com/@williford?source=post_page---byline--2073dfb33d7e--------------------------------)[![Jonathan R. Williford, PhD](../Images/63b57be5ef10621c8d48b93399b2b598.png)](https://medium.com/@williford?source=post_page---byline--2073dfb33d7e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--2073dfb33d7e--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--2073dfb33d7e--------------------------------) [Jonathan R. Williford, PhD](https://medium.com/@williford?source=post_page---byline--2073dfb33d7e--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--2073dfb33d7e--------------------------------) ·阅读时长 8 分钟 ·2024年6月19日

--

![](../Images/727ab5bfb53e278b694bc22ca7d293aa.png)

图像由作者使用 Dall-E 3 生成。

最近的多模态变换器网络，如 CLIP（Radford 等，2021）和 LLaVA（Liu 等，2023），与大脑相比如何？这些网络中的注意力机制与大脑之间是否存在相似之处？在本文中，我将从相似性和差异性的角度，分析这些变换器架构与哺乳动物大脑的对比。

令我印象深刻的是，视觉变换器、CLIP 和 LLaVA 执行一种类似于大脑前注意性视觉处理的处理方式。这种处理发生在对刺激的初步前馈视觉响应中，在回归之前完成。虽然前馈处理可以完成很多任务，但研究表明，大脑中的前馈前注意性处理确实存在困难：

1.  区分相似类型对象的身份或特征，尤其是当物体靠得很近或杂乱无章，或者物体是非自然或人造的（VanRullen 2007）。

1.  更复杂的任务，如计数、迷宫或曲线追踪任务。

1.  感知那些较难看到的物体，例如在难以分辨物体边界的情况下。

与前馈处理相比，大脑的一大特点是不同区域之间的互动丰富性，我将在下一节中详细讨论这一点。

# 大脑中的双向活动

在大多数当前的深度学习架构中，活动是单向传播的，例如，图像可能作为输入提供给网络，然后从一层传播到下一层，直到得到分类作为输出。

![](../Images/f7764a163e02f6e5b19857a4dc11aace.png)

图1：简化图示，展示了猕猴大脑中一些前馈和反馈连接。较早（或较低层次）的区域为白色，而较晚（或较高层次）的区域为蓝色。图像由作者提供。

大脑比这些前馈模型更加有趣。在视觉系统中，刺激最初会以前馈方式从较低层次的视觉区域传播到较高层次的视觉区域，随后更高层次的区域会对较低层次的区域施加影响，如图1所示。

这种反馈的一部分是有意识的自上而下的注意力，它使我们能够为感兴趣的物体和特征分配更多的资源，并消除复杂或模糊刺激的歧义。另一部分反馈是自动的，它使得更高层次的区域能够向较低层次的区域传递通过前馈方式无法获得的信息。

有意识的自上而下的注意力被认为有助于视觉刺激的意识。如果无法有意识地访问编码边界和边缘的低层次区域，我们将无法对边界进行如此精确的空间感知。像是心算曲线或解迷这样的任务将变得不可能。

自动无意识反馈的一个例子是边界归属编码，这在视觉区域V2中的大约一半方向选择性神经元中可见（Zhou等，2000；Williford和von der Heydt，2013）。这些神经元会在大约40毫秒内编码局部信息，并且在最初反应后的10毫秒内，便会结合全局背景来解决遮挡问题——保留关于哪些物体通过遮挡其背景来创建边界的信息。

这种无意识反馈的另一个例子是Poort等人（2012）通过图2所示的图像展示的。在猕猴初级视觉皮层V1中，神经元通常会在刺激呈现后的50-75毫秒内，首先编码它们感受野内的局部特征（例如绿色方块）。然而，在大约75毫秒后，它们会接收到来自更高层次区域的反馈，并且当纹理属于某个形状时，它们的反应会更强烈，比如上面这种纹理定义的形状。即使注意力从形状上转移，这种情况仍然会发生；然而，如果猴子集中注意力在形状上，神经元的反应通常会更强。

![](../Images/16166897fa5998f9ab7213eeddd075d0.png)

图2：仅由纹理定义的形状，如上图，可能很难通过纯粹的“前馈”方式来感知。低层次和高层次区域之间的互动使我们能够感知这些难以看清的形状（Poort等，2012）。图像由作者提供。

一种看待这种双向交互的方式是，每个神经元都会不断贪婪地利用所有可用的预测信号。即使是较高层次的区域也可以进行预测，特别是当视觉边界不对应显著的一阶对比边缘时。

# 变压器

随着transformer的引入（Vaswani等人，2017年）以及能够一次生成一个单词的能力，可能会让你认为transformer是递归的。然而，transformer的各个步骤之间并没有保持内部状态，只有前一个输出作为输入。因此，递归是有限的，并没有大脑中普遍存在的双向性。transformer确实具有多头注意力机制，这就像是能够同时关注一定数量的事物（原文中是8个）。因此，图像transformer可以看作是类似于带有一些修改的前注意力前馈处理。

# CLIP

![](../Images/5fd93c045205ef5f4738c2c0dbca308a.png)

图3：CLIP通过*图像说明配对*训练图像和文本编码器。*I*₁和*T*₁分别是图像1和对应说明的编码。使用对比学习损失函数，使得当*i*=*j*时，*I*ᵢ和*Tj*更加相似，当*i*≠*j*时，它们更加不相似。权重是从头开始训练的。图示已获得[Radford等人(2021)](http://proceedings.mlr.press/v139/radford21a)的许可。

OpenAI的Radford及其同事在2021年的论文《从自然语言监督中学习可转移的视觉模型》中介绍了CLIP。CLIP背后的思想非常简单，如图3所示。它从互联网上获取一堆图像和说明配对，将图像输入到图像编码器，将文本输入到文本编码器。然后，它使用一个损失函数，当图像和文本在同一对中时，使图像编码和文本编码更加接近，否则损失函数则增加它们编码之间的距离。这就是CLIP的作用：能够比较文本和图像之间的相似性。这也使得它可以用于零-shot分类，如图4所示。CLIP本身并不从图像生成文本描述。

图像编码器和文本编码器是独立的，这意味着没有办法通过任务驱动的调制来影响图像编码。这意味着图像编码器必须编码所有可能与任务相关的内容。通常，输入图像的分辨率较小，这有助于防止计算和内存需求的激增。

![](../Images/450a9b45845992be3d2750574f68b548.png)

图4：CLIP可以用于零-shot分类。为每个类别生成文本，然后将其编码为标记*T*1…*TN*。接着对图像进行编码，并与生成的文本编码进行相似度比较。最相似的文本编码即为选定的类别。图示已获得[Radford等人(2021)](http://proceedings.mlr.press/v139/radford21a)的许可。

# LLaVA

![](../Images/cbc5edb3e0e90bee04a87b00ca26bded.png)

图 5：LLaVA 架构。X*v*：图像，Xq：指令/问题，H*v*：图像令牌，Hq：指令令牌，Xa：答案，一次生成一个令牌。图片由作者提供，基于[Liu et al. (2023)](https://doi.org/10.48550/arXiv.2304.08485)中的图 1。

大型语言与视觉助手（LLaVA）（Liu et al. 2023）是一种大型语言与视觉架构，它扩展并建立在 CLIP 的基础上，加入了描述和回答图像问题的能力。这种架构让我感兴趣，因为它可以尝试一些神经科学和心理学中使用的任务。

LLaVA 使用由 CLIP 训练的视觉变换器模型 ViT-L/14 进行图像编码（图 5）。第一篇论文使用一个线性投影矩阵 W，将编码转换为令牌。从图像 Hᵥ 和文本指令 Hq 计算出的令牌作为输入提供给 LLaVA。然后，LLaVA 可以一次生成一个语言回应 Xa，并将目前的回应附加到下一个迭代的输入中。

我不会详细讨论 LLaVA 的训练方式，但有趣的是，他们如何使用 ChatGPT 来扩展图 5 中的标题（Xc），形成关于图像的指令（Hq）和回应（用于训练 Xa），并使用边界框信息。

在 LLaVA 的版本 1.5（Liu et al. 2024）中，他们所做的一些改进包括：

+   线性投影矩阵 W 被一个多层感知机替代。

+   通过使用一个图像编码器，图像的分辨率得到了提高，该编码器接收大小为 336x336 像素的图像，并将图像拆分为网格，每个网格分别编码。

大脑中的任务驱动注意力可以动态地将资源分配给感兴趣的物体、位置或特征，这使得能够处理那些本来会被杂乱或其他物体淹没的信息。在 LLaVA 中，图像编码器与文本指令是独立的，因此为了成功，它需要确保任何潜在有用的信息都存储在图像令牌（Hᵥ）中。

# 结论

LLaVA 和 CLIP 缺乏双向和递归的内部状态，这限制了它们的处理能力。特别是在图像处理方面，图像处理与文本指令是独立进行的。大多数卷积神经网络也有这些限制。这让我产生了我的推测：

> *推测：大多数卷积神经网络、视觉变换器和多模态变换器网络的处理方式类似于大脑中前注意力的前馈视觉处理。*

这不仅仅是批评，更是一种可以提供信息的洞察。前馈处理可以做很多事情，且速度很快。然而，它在可以使用哪些资源方面并不像预期的那么动态，这可能导致在杂乱场景中出现信息瓶颈，并且在没有大幅增加编码尺寸的情况下，无法为复杂任务编码足够的信息。创建前馈式工作的模型是一个重要的踏脚石，因为添加递归和双向处理的难度很大。

一些网络不限于预注意力前馈网络，但目前大多数架构落后于变压器架构。这些包括长短期记忆模型（LSTM），以及最近的Mamba架构，它比变压器有多个优势（[Gu 和 Dao 2024](http://neural.vision/blog/neuroai/CLIP-LLaVA-and-the-Brain/#ref-guMamba2024)）。扩展型LSTM（[Beck 等 2024](http://neural.vision/blog/neuroai/CLIP-LLaVA-and-the-Brain/#ref-beckXLSTM2024)，[Alkin 等 2024](http://neural.vision/blog/neuroai/CLIP-LLaVA-and-the-Brain/#ref-alkinVisionLSTM2024)）最近被提出，这有助于缩小变压器和LSTM之间的差距。扩散模型也有一种有限的递归类型，它使用图像作为迭代之间的状态。

# 参考文献

B. Alkin, M. Beck, K. Pöppel, S. Hochreiter, 和 J. Brandstetter, [Vision-LSTM: xLSTM 作为通用视觉主干](http://arxiv.org/abs/2406.04303) (2024), [http://arxiv.org/abs/2406.04303](http://arxiv.org/abs/2406.04303).

M. Beck, K. Pöppel, M. Spanring, A. Auer, O. Prudnikova, M. Kopp, G. Klambauer, J. Brandstetter, 和 S. Hochreiter, [xLSTM: 扩展型长短期记忆网络](http://arxiv.org/abs/2405.04517) (2024), [http://arxiv.org/abs/2405.04517](http://arxiv.org/abs/2405.04517)

A. Gu 和 T. Dao. [Mamba: 线性时间序列建模与选择性状态空间](http://arxiv.org/abs/2312.00752) (2024) [http://arxiv.org/abs/2312.00752](http://arxiv.org/abs/2312.00752)

H. Liu, C. Li, Y. Li, 和 Y. J. Lee “[通过视觉指令调优改进的基准](https://openaccess.thecvf.com/content/CVPR2024/html/Liu_Improved_Baselines_with_Visual_Instruction_Tuning_CVPR_2024_paper.html) (2024) IEEE/CVF CVPR 论文集*。

H. Liu, C. Li, Q. Wu, 和 Y. J. Lee, [视觉指令调优](https://doi.org/10.48550/arXiv.2304.08485) (2023), [https://doi.org/10.48550/arXiv.2304.08485](https://doi.org/10.48550/arXiv.2304.08485)

J. Poort, F. Raudies, A. Wannig, V. A. F. Lamme, H. Neumann, 和 P. R. Roelfsema. [注意力在视觉皮层V1和V4区中的图形与背景分离中的作用](https://doi.org/10.1016/j.neuron.2012.04.032) (2012) Neuron

A. Radford, J. W. Kim, C. Hallacy, A. Ramesh, G. Goh, S. Agarwal, G. Sastry, A. Askell, P. Mishkin, 和 J. Clark. [从自然语言监督中学习可迁移的视觉模型](http://proceedings.mlr.press/v139/radford21a) (2021) ICML

R. VanRullen, [《前馈波动的力量》](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2864977/) (2007) 《认知心理学进展》

A. Vaswani, N. Shazeer, N. Parmar, J. Uszkoreit, L. Jones, A. N. Gomez, Ł. Kaiser, 和 I. Polosukhin, [《注意力机制就是你所需要的一切》](https://proceedings.neurips.cc/paper/7181-attention-is-all) (2017) NeurIPs

J. R. Williford 和 R. von der Heydt, [《边界所有权编码》](http://scholarpedia.org/article/Border-ownership_coding) (2013) Scholarpedia

H. Zhou, H. S. Friedman, 和 R. von der Heydt. “[《猴子视觉皮层中的边界所有权编码》](https://www.jneurosci.org/content/20/17/6594.full) (2000) 《神经科学杂志》

*原文发布于* [*http://neural.vision*](http://neural.vision/blog/neuroai/CLIP-LLaVA-and-the-Brain/) *于2024年6月19日。*
