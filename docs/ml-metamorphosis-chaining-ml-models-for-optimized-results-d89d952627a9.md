# ML变形：通过串联ML模型实现优化结果

> 原文：[https://towardsdatascience.com/ml-metamorphosis-chaining-ml-models-for-optimized-results-d89d952627a9?source=collection_archive---------2-----------------------#2024-10-23](https://towardsdatascience.com/ml-metamorphosis-chaining-ml-models-for-optimized-results-d89d952627a9?source=collection_archive---------2-----------------------#2024-10-23)

## 知识蒸馏、模型压缩和规则提取的普遍原理

[](https://medium.com/@vadim.arzamasov?source=post_page---byline--d89d952627a9--------------------------------)[![Vadim Arzamasov](../Images/70ced2eafa6fc926052979875a0a4265.png)](https://medium.com/@vadim.arzamasov?source=post_page---byline--d89d952627a9--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--d89d952627a9--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--d89d952627a9--------------------------------) [Vadim Arzamasov](https://medium.com/@vadim.arzamasov?source=post_page---byline--d89d952627a9--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--d89d952627a9--------------------------------) ·阅读时长 7 分钟·2024年10月23日

--

![](../Images/5b5804cb68224d5d689f156155d59eb9.png)

**图 1**。此图及其他图像由作者在recraft.ai的帮助下创建

机器学习（ML）模型训练通常遵循一个熟悉的流程：首先收集数据，清理并准备数据，然后进行模型拟合。但如果我们能将这一过程进一步推进呢？就像一些昆虫在达到成熟之前会经历剧烈的变化一样，ML模型也可以以类似的方式进化（参见Hinton等人[1]）——我将其称为**ML变形**。这个过程涉及将不同的模型串联在一起，最终生成的模型比直接从头开始训练的模型质量要好得多。

其工作原理如下：

+   从一些初步知识开始，*数据 1*。

+   在这些数据上训练一个机器学习模型，*模型 A*（例如神经网络）。

+   使用*模型 A*生成新的数据，*数据 2*。

+   最后，使用数据 2 来拟合你的目标模型，*模型 B*。

![](../Images/2645f7d1e0777997a8daebcec3b49575.png)

**图 2**。ML 变形的示意图

你可能已经熟悉这一概念，知识蒸馏就是用一个较小的神经网络替换较大的神经网络。但ML变形更进一步，初始模型（*模型 A*）和最终模型（*模型 B*）不必是神经网络。

## 示例：MNIST数据集上的ML变形

*想象一下，你的任务是使用MNIST手写数字图像数据集训练一个多类决策树，但只有1,000张图像有标签。你可以直接在这个有限的数据上训练决策树，但准确度大约只能达到0.67。这并不好，对吧？或者，你可以使用机器学习变换方法来提高结果。*

但在深入探讨解决方案之前，我们先快速回顾一下支持这种方法的技术和研究。

## 1\. 知识蒸馏（2015）

即使你没有使用知识蒸馏，你也可能见过它的应用。例如，Meta建议蒸馏其Llama 3.2模型，以便将其适配到特定任务[2]。或者看看DistilBERT——一个蒸馏版的BERT[3]，或者DMD框架，它通过蒸馏Stable Diffusion来加速图像生成速度，提升了30倍[4]。

知识蒸馏的核心是将知识从一个大型、复杂的模型（*教师*）传递给一个较小、效率更高的模型（*学生*）。该过程包括创建一个*转移集*，该集包含原始训练数据和由教师模型伪标注的额外数据（无论是原始数据还是合成数据）。这些伪标签被称为*软标签*——它们源自教师模型在多个类别上的预测概率。这些软标签提供了比*硬标签*（简单的类别指示符）更丰富的信息，因为它们反映了教师的信心，并捕捉到类别之间的微妙相似性。例如，它们可能表明一个特定的“1”比“5”更像“7”。

通过在这个丰富的转移集上进行训练，学生模型可以有效地模仿教师模型的表现，同时更加轻便、快速且易于使用。

> 以这种方式获得的学生模型比仅在原始训练集上训练得到的模型更准确。

## 2\. 模型压缩（2007）

模型压缩[5]通常被视为知识蒸馏的前奏，但两者之间存在重要差异。与知识蒸馏不同，模型压缩似乎并未使用软标签，尽管文献中有一些相关说法[1,6]。我没有找到任何证据表明软标签是过程的一部分。事实上，原始论文中的方法甚至不依赖于人工神经网络（ANNs）作为*模型A*。相反，它使用了一组模型——如支持向量机（SVMs）、决策树、随机森林等。

模型压缩通过逼近特征分布*p(x)*来创建转移集。然后，这个集由*模型A*标注，提供条件分布*p(y|x)*。原始工作中的关键创新是一种名为MUNGE的技术，用于逼近*p(x)*。与知识蒸馏一样，目标是训练一个较小、更高效的*模型B*，并保留较大*模型A*的性能。

> 正如知识蒸馏中所示，这种训练方式得到的压缩模型，通常能够超过直接在原始数据上训练的类似模型，因为传递集中嵌入了丰富的信息[5]。

通常，“模型压缩”一词被更广泛地用来指代任何减少*模型 A*大小的技术[7,8]。这包括像知识蒸馏这样的技术，也包括不依赖传递集的技术，如修剪、量化或神经网络的低秩近似。

## 3. 规则提取（1995）

当问题不在于计算复杂度或内存，而在于模型决策过程的不透明性时，教育性规则提取提供了一种解决方案[9]。在这种方法中，训练一个更简单、更易解释的模型（*模型 B*）来复制不透明教师模型（*模型 A*）的行为，目的是推导出一组人类可读的规则。这个过程通常从将未标记的示例（通常是随机生成的）输入到*模型 A*开始，*模型 A*对这些示例进行标记，生成一个传递集。然后，使用这个传递集来训练透明的学生模型。例如，在分类任务中，学生模型可能是一个决策树，它输出如下规则：“如果特征 X1 大于阈值 T1 且特征 X2 小于阈值 T2，则分类为正类”。

教育性规则提取的主要目标是紧密模仿教师模型的行为，以*保真度*为衡量标准——即学生模型与教师模型之间的准确性，作为主要的质量衡量标准。

> 有趣的是，研究表明，通过这种方法创建的透明模型，有时能够比直接在用于构建*模型 A*的原始数据上训练的类似模型达到更高的准确率[10,11]。

教育性规则提取属于被称为“全局”模型解释方法的更广泛技术家族，这其中还包括分解性和折衷规则提取。更多详情请参见[12]。

## 4. 仿真作为模型 A

*模型 A*不一定是一个机器学习模型——它也可以是一个经济或物理过程的计算机仿真，例如模拟飞机机翼周围的气流。在这种情况下，*数据 1*由定义该过程的微分方程或差分方程组成。对于任何给定的输入，仿真通过数值解这些方程来做出预测。然而，当这些仿真变得计算开销较大时，就需要一种更快速的替代方案：一个代理模型（*模型 B*），它可以加速诸如优化之类的任务[13]。当目标是识别输入空间中的重要区域，例如系统稳定性的区域时，开发一个可解释的*模型 B*，这一过程称为情境发现[14]。为了生成用于代理建模和情境发现的传递集（*数据 2*），*模型 A*会在一个多样化的输入集上运行。

## 回到我们的 MNIST 示例

在TDS上的一篇深刻的[文章](/teaching-your-model-to-learn-from-itself-8b5ef13eb173)中，[Niklas von Moers](https://medium.com/u/a3ecf86934da?source=post_page---user_mention--d89d952627a9--------------------------------)展示了半监督学习如何提高卷积神经网络（CNN）在相同输入数据上的表现。这个结果适用于ML变换流水线的第一阶段，在该阶段，*Model A*是一个经过训练的CNN分类器。转移集*Data 2*包含原本标注的1,000个训练样本以及约55,000个由*Model A*高置信度预测的伪标注样本。接下来，我在*Data 2*上训练我们的目标*Model B*，一个决策树分类器，并达到了0.86的准确率——远高于仅在*Data 1*的标注部分训练时的0.67。这意味着，将决策树与CNN解决方案链式连接，将决策树的错误率从0.33降低到了0.14。相当大的提升，是吧？

查看完整的实验代码，请访问 [GitHub](https://github.com/Arzik1987/medium/tree/main/metamorphosis) 仓库。

## **结论**

总结来说，ML变换并非总是必要的——特别是当你的唯一关注点是准确性，并且不需要可解释性、更快的推理或减少存储需求时。但在其他情况下，链式模型可能会比直接在原始数据上训练目标模型产生显著更好的结果。

![](../Images/1b04e87426dec01924b73226bb1df2ea.png)

**图 2**：为方便参考，图示再次呈现

对于分类任务，过程包括：

+   *Data 1*：原始的，完全或部分标注的数据。

+   *Model A*：在*Data 1*上训练的模型。

+   *Data 2*：包括伪标注数据的转移集。

+   *Model B*：最终模型，旨在满足额外要求，如可解释性或效率。

那么，为什么我们不总是使用ML变换呢？挑战通常在于找到合适的转移集，*Data 2* [9]。但这是另一个话题。

## 参考文献

[1] Hinton, Geoffrey. “[蒸馏神经网络中的知识](https://arxiv.org/pdf/1503.02531)。” *arXiv预印本arXiv:1503.02531* (2015年)。

[2] [介绍 Llama 3.2](https://llama.meta.com/?ref=engineering.fb.com)

[3] Sanh, Victor 等人. “[DistilBERT：BERT的蒸馏版：更小、更快、更便宜、更轻量](https://arxiv.org/abs/1910.01108)。” *arXiv预印本arXiv:1910.01108* (2019年)。

[4] Yin, Tianwei 等人. “[一步扩散与分布匹配蒸馏](https://tianweiy.github.io/dmd/)。” *IEEE/CVF计算机视觉与模式识别会议论文集*。2024年。

[5] Buciluǎ, Cristian, Rich Caruana, 和 Alexandru Niculescu-Mizil. “[模型压缩](https://dl.acm.org/doi/pdf/10.1145/1150402.1150464?casa_token=CY8nr3ZTpi0AAAAA%3A6T9MQ4MKzDCllOBuaurkddgR67bKLt88tc-TtEf0MfzNCdncFzr0Q1ZQZSha2GkBBYdysx78qMxdwA)” *第12届ACM SIGKDD国际会议《知识发现与数据挖掘》论文集*，2006年。

[6] [知识蒸馏](https://en.wikipedia.org/wiki/Knowledge_distillation)，维基百科

[7] [太空深度学习模型压缩技术概述](https://medium.com/gsi-technology/an-overview-of-model-compression-techniques-for-deep-learning-in-space-3fd8d4ce84e5)，发表于Medium

[8] [使用未标注的问答数据集蒸馏BERT](/distilling-bert-using-unlabeled-qa-dataset-4670085cc18)，发表于Towards Data Science

[9] Arzamasov, Vadim, Benjamin Jochum, 和 Klemens Böhm. “[教育规则提取以学习可解释模型 — 一项实证研究](https://arxiv.org/pdf/2112.13285).” *arXiv 预印本 arXiv:2112.13285* (2021).

[10] Domingos, Pedro. “[通过多模型从示例中获取知识](https://citeseerx.ist.psu.edu/document?repid=rep1&type=pdf&doi=1a9a39da9d4fc937bc455705d508674a205620aa)” *MACHINE LEARNING-INTERNATIONAL WORKSHOP THEN CONFERENCE-*. MORGAN KAUFMANN PUBLISHERS, INC., 1997.

[11] De Fortuny, Enric Junque, 和 David Martens. “[基于主动学习的教学规则提取](https://ieeexplore.ieee.org/abstract/document/7018925?casa_token=SU8qtZ-ZkGEAAAAA%3A7L_-_Sj-eZ7x0_f4oYQgH4kynTftYbJW4ytvcEQgaq-r4VWyOdmh0lD1jXYN1otMmKzt-dIRcA).” *IEEE 神经网络与学习系统交易* 26.11 (2015): 2664–2677.

[12] Guidotti, Riccardo, 等. “[解释黑盒模型的方法调查](https://dl.acm.org/doi/abs/10.1145/3236009).” *ACM 计算机调查 (CSUR)* 51.5 (2018): 1–42.

[13] [代理模型](https://en.wikipedia.org/wiki/Surrogate_model)，维基百科

[14] [Python中的情景发现](https://waterprogramming.wordpress.com/2015/08/05/scenario-discovery-in-python/)，[Water Programming](https://waterprogramming.wordpress.com/)上的博客文章

[15] [让模型从自身学习](/teaching-your-model-to-learn-from-itself-8b5ef13eb173)，发表于Towards Data Science
