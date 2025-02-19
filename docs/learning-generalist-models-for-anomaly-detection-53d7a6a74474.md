# 学习通用模型用于异常检测

> 原文：[`towardsdatascience.com/learning-generalist-models-for-anomaly-detection-53d7a6a74474?source=collection_archive---------5-----------------------#2024-04-14`](https://towardsdatascience.com/learning-generalist-models-for-anomaly-detection-53d7a6a74474?source=collection_archive---------5-----------------------#2024-04-14)

[](https://medium.com/@guansong-pang?source=post_page---byline--53d7a6a74474--------------------------------)![Guansong Pang](https://medium.com/@guansong-pang?source=post_page---byline--53d7a6a74474--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--53d7a6a74474--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--53d7a6a74474--------------------------------) [Guansong Pang](https://medium.com/@guansong-pang?source=post_page---byline--53d7a6a74474--------------------------------)

·发布于[Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--53d7a6a74474--------------------------------) ·阅读时间：9 分钟·2024 年 4 月 14 日

--

> 通用异常检测（GAD）的目标是训练一个单一的检测模型，使其能够在不同应用领域的多样化数据集中泛化检测异常，而无需在目标数据上进行进一步训练。
> 
> 该工作将于 CVPR 2024 发布[1]。

# 概述

最近的研究表明，大型预训练视觉-语言模型（VLMs）如 CLIP，在从不同数据集中检测工业缺陷时具有强大的泛化能力，但它们的方法过于依赖于手工制作的缺陷文本提示，这使得它们难以在其他应用中泛化，例如医疗影像异常或自然图像中的语义异常。

在本研究中，我们提出了通过少量正常图像作为样本提示，快速训练一个 GAD 模型，以便在多样化的数据集上进行异常检测。为此，我们引入了一种新方法，学习一种**上下文**残差**学习**模型来实现 GAD，称为**InCTRL**。

它在一个辅助数据集上进行训练，通过查询图像和少量正常样本提示之间的残差的整体评估，来区分异常和正常样本。不论数据集如何，按照异常的定义，异常样本的残差通常会比正常样本更大，从而使 InCTRL 能够在不同领域之间进行泛化，无需进一步训练。

在九个 AD 数据集上进行全面实验，以建立一个 GAD 基准，涵盖了工业缺陷异常、医学异常和语义异常的检测，涉及一对多和多类设置，其中 InCTRL 表现最佳，显著优于现有的最先进竞争方法。代码可在[`github.com/mala-lab/InCTRL`](https://github.com/mala-lab/InCTRL)获取。

# 引言

异常检测（AD）是一个重要的计算机视觉任务，旨在检测那些与数据集中大多数样本显著偏离的样本，这一任务具有广泛的实际应用，例如工业检测、医学影像分析和科学发现等[2–3]。当前的 AD 范式集中于在每个目标数据集的训练数据上单独构建一个模型，例如无异常样本集的数据重建方法、一类分类和知识蒸馏方法。尽管这些方法在各种 AD 基准测试中表现出了显著的检测性能，但它们需要大量的训练数据以及每个数据集上需要熟练的检测模型训练。因此，在某些应用场景下，它们变得不可行，尤其是在由于数据隐私问题（例如，因使用这些数据训练模型而导致的机器反学习问题[3]）或新应用部署中缺乏大规模训练数据等原因，无法对目标数据集进行训练的情况下。为了应对这些挑战，本研究探讨了*通用异常检测（GAD）*模型的问题，旨在训练一个单一的检测模型，该模型能够在不同应用领域的多样数据集中进行异常检测，而无需对目标数据进行任何训练。

大型视觉语言模型（VLMs），如 CLIP，已经在网页规模的图像-文本数据上进行预训练，近年来表现出卓越的泛化能力，能够在不同的数据集上实现准确的视觉识别，而无需对目标数据进行任何微调或适配。更重要的是，一些非常近期的研究（例如，WinCLIP [5]）表明，这些 VLMs 也可以用来在不同的缺陷检测数据集上实现显著的泛化。然而，这些模型的一个重要限制是它们依赖于大量手工制作的、特定于缺陷的提示。这种依赖限制了它们的适用性，使得将其扩展到检测其他数据领域的异常变得具有挑战性，例如医学影像中的异常或在一对多或多类设置中的语义异常。

为了解决这个问题，我们提出训练一个 GAD 模型，该模型旨在利用来自任何目标数据集的少量正常图像作为样本提示，支持快速的 GAD，如图 1（顶部）所示。少量样本的设置源于一个事实，即在实际应用中，获取少量正常图像通常很容易。此外，这些少量样本并不用于模型训练/调优；它们仅作为样本提示，在推理过程中用于支持测试图像的异常评分。这个公式与当前的少量样本 AD 方法根本不同，后者使用这些目标样本及其扩展版本来训练检测模型，这可能导致目标数据集的过拟合，无法泛化到其他数据集，如图 1（底部）所示。

![](img/3fe0dead46f46cfcbebafc24c90de4c6.png)

我们接着介绍了一种 GAD 方法，这是首个此类方法，通过基于 CLIP 学习一个**in**-**c**on**t**ext **r**esidual **l**earning 模型，命名为**InCTRL**。它训练一个 GAD 模型，通过学习识别查询图像与一组来自辅助数据的少量正常图像之间的残差/差异，从而区分异常与正常样本。这些少量正常图像，亦即**上下文样本提示**，作为*正常模式的原型*。根据异常的定义，与这些正常模式的特征进行比较时，异常样本通常会产生比正常样本更大的残差，因此所学的上下文残差模型能够在不同领域的数据集上泛化，检测到各种类型的异常。为了更好地捕捉残差，**InCTRL**在图像和图块层面建模上下文残差，深入理解什么构成了异常。此外，我们的上下文残差学习还能够无缝地将正常/异常的文本提示引导的先验知识融入检测模型，为来自文本-图像对齐语义空间的检测提供额外的优势。

我们在九个 AD 数据集上进行了广泛的实验，以建立一个包含三种流行 AD 任务的 GAD 基准，包括工业缺陷异常检测、医学图像异常检测和语义异常检测，涵盖了单一对抗与多类设置。我们的结果表明，**InCTRL**显著超过了现有的最先进方法。

# 方法

我们的方法**InCTRL**旨在有效地建模查询图像与一组少量正常图像之间的上下文残差，利用 CLIP 的泛化能力，检测来自不同应用领域的异常残差。

CLIP 是一个视觉语言模型（VLM），由文本编码器和视觉编码器组成，通过在大规模的文本-图像数据上进行预训练，使得来自这些编码器的图像和文本表示得到良好的对齐。**InCTRL**通过在图像编码器中使用上下文残差学习，结合辅助数据进行优化，并通过文本编码器提供的文本提示引导的先验知识增强学习。

更具体地，如图 2 所示，我们首先模拟一个包含一个查询图像**x**和一组少量正常样本提示**P'**的上下文学习示例，这些图像和提示均随机从辅助数据中采样。然后通过视觉编码器，我们执行多层次的补丁级别和图像级别的残差学习，分别捕捉查询图像与少量正常样本提示之间的局部和全局差异。此外，我们的模型能够无缝地将来自文本编码器的正常和异常文本提示引导的先验知识融入到学习过程中，这些文本提示嵌入与查询图像之间的相似度用于指导学习。**InCTRL**的训练是优化附加在视觉编码器上的少量投影/适应层，使得训练数据中的异常样本相较于正常样本具有更大的异常分数，同时保持两个编码器中的原始参数冻结；在推理过程中，将测试图像与目标数据集中的少量正常图像提示和文本提示一起输入到我们改进的基于 CLIP 的 GAD 网络中，其输出为测试图像的异常分数。

![](img/b71f47a82528a9d0f1aa0512687df9c8.png)

# 实验结果

**数据集与评估指标。** 为了验证我们方法的有效性，我们在九个真实世界的异常检测数据集上进行了全面实验，包括五个工业缺陷检测数据集（MVTec AD, VisA, AITEX, ELPV, SDD）、两个医学影像数据集（BrainMRI, HeadCT），以及两个语义异常检测数据集：MNIST 和 CIFAR-10，采用一对多和多类协议。在一对多协议下，选择一个类作为正常类，其余类作为异常类；而在多类协议下，MNIST 中的偶数类和 CIFAR-10 中的与动物相关的类被视为正常类，其余类的图像被视为异常。

为了评估 GAD 性能，使用 MVTec AD 及其训练和测试集的组合作为辅助训练数据，在此上训练 GAD 模型，并随后在其他八个数据集的测试集上进行评估，无需进一步训练。在评估 MVTec AD 上的性能时，我们在 VisA 上训练模型。

针对目标数据的少量常规提示是从目标数据集的训练集中随机采样的，并且对于所有模型保持一致，以确保公平比较。我们评估了少量常规提示集为 K = 2, 4, 8 时的性能。报告的结果是基于三次独立实验的平均值，且每次实验使用不同的随机种子。

关于评估指标，我们使用两个流行的指标 AUROC（接收操作特征曲线下面积）和 AUPRC（精准率-召回率曲线下面积）来评估 AD 性能。

**结果。** 主要结果见表格 1 和表格 2。对于 11 个工业缺陷检测（AD）数据集，**InCTRL**在几乎所有的情况中，在三种少量样本设置下，均显著优于所有竞争模型，无论是在 AUROC 还是 AUPRC 方面。随着更多少量样本图像提示的加入，所有方法的表现普遍得到提升。**InCTRL**能够很好地利用增加的少量样本，并保持相对于竞争方法的优势。

![](img/3315761a9681154f009c7313b51c54e2.png)![](img/541a7bcbe2af78ff4e3fb584a8151144.png)

**消融研究。** 我们考察了方法中三个关键组件对泛化能力的贡献：文本提示引导的特征（T）、补丁级残差（P）和图像级残差（I），以及它们的组合。结果见表格 3。实验结果表明，对于工业缺陷 AD 数据集，视觉残差特征在比文本提示特征更为重要，尤其是在像 ELPV、SDD 和 AITEX 这样的数据集上。在医学图像 AD 数据集上，视觉残差和文本知识对性能提升都有显著贡献，表现出互补关系。在语义 AD 数据集上，结果主要受到补丁级残差和/或文本提示特征的影响。重要的是，我们的三个组件通常是互相补充的，从而使得在各数据集上的检测泛化能力更强。

![](img/4abc7255039bcdccd2675268acada161.png)

**In-context 残差学习的重要性。** 为了评估**InCTRL**中残差学习的重要性，我们在多层补丁级和图像级残差学习中尝试了两种替代操作：1）将残差操作替换为**拼接**操作，2）将残差操作替换为**平均**操作，其他**InCTRL**组件保持不变。如表格 3 所示，in-context 残差学习的泛化能力明显优于这两种替代方式，显著提升了该模型在 GAD 任务中的性能，且跨三个不同的领域表现良好。

# 结论

本文提出了一个 GAD 任务，用于评估异常检测方法在不同场景下识别异常的泛化能力，且无需在目标数据集上进行任何训练。这是首个专注于通用异常检测方法的研究，涵盖了工业缺陷、医学异常和语义异常。随后，我们提出了一种方法，称为**InCTRL**，在少样本设置下解决此问题。**InCTRL**通过整体上下文残差学习实现了卓越的 GAD 泛化能力。在九个异常检测数据集上进行了广泛实验，为上述三种流行的异常检测任务建立了 GAD 评估基准，在多个少样本设置下，**InCTRL**显著且持续地超越了现有最先进的竞争模型。

*请查阅完整论文[1]以获取更多关于方法和实验的详细信息。代码可以在* [`github.com/mala-lab/InCTRL`](https://github.com/mala-lab/InCTRL) *公开获取。*

# 参考文献

[1] Zhu, Jiawen, 和 Guansong Pang. “通过少样本提示的上下文残差学习实现通用异常检测。” *arXiv 预印本 arXiv:2403.06495* (2024)。

[2] Pang, Guansong, 等. “深度学习在异常检测中的应用：综述。” *ACM 计算机综述（CSUR）* 54.2 (2021)：1–38。

[3] Cao, Yunkang, 等. “视觉异常检测综述：挑战、方法和前景。” *arXiv 预印本 arXiv:2401.16402* (2024)。

[4] Xu, Jie, 等. “机器遗忘：解决方案与挑战。” *IEEE《计算智能新兴话题》期刊* (2024)。

[5] Jeong, Jongheon, 等. “Winclip: 零样本/少样本异常分类和分割。” *IEEE/CVF 计算机视觉与模式识别会议论文集*。2023。
