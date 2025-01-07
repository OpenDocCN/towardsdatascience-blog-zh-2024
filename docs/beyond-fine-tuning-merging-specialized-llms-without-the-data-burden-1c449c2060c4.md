# 超越微调：合并专业化LLM而不增加数据负担

> 原文：[https://towardsdatascience.com/beyond-fine-tuning-merging-specialized-llms-without-the-data-burden-1c449c2060c4?source=collection_archive---------5-----------------------#2024-08-13](https://towardsdatascience.com/beyond-fine-tuning-merging-specialized-llms-without-the-data-burden-1c449c2060c4?source=collection_archive---------5-----------------------#2024-08-13)

## 从模型融合到自动进化合并：利用专业化LLM融合以减少数据需求并消除大量的微调。

[](https://medium.com/@InfiniteLearningLoop?source=post_page---byline--1c449c2060c4--------------------------------)[![Elahe Aghapour](../Images/47a2023c566d50d8ecfcafdb69bb9bb7.png)](https://medium.com/@InfiniteLearningLoop?source=post_page---byline--1c449c2060c4--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--1c449c2060c4--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--1c449c2060c4--------------------------------) [Elahe Aghapour](https://medium.com/@InfiniteLearningLoop?source=post_page---byline--1c449c2060c4--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--1c449c2060c4--------------------------------) ·10分钟阅读·2024年8月13日

--

**作者：** [**Elahe Aghapour**](https://medium.com/u/75214fb27311?source=post_page---user_mention--1c449c2060c4--------------------------------)**，** [**Salar Rahili**](https://medium.com/u/6dff1eb2cc9f?source=post_page---user_mention--1c449c2060c4--------------------------------)

# 引言：

计算机视觉和自然语言处理领域正在迅速发展，这导致对为特定下游任务微调的专业化模型的需求不断增长。然而，拥有多个不同的微调模型也带来了多个缺点：

1. 每个任务都必须存储和部署一个单独的模型（这个问题可以通过应用像LoRA这样的微调方法来解决）。

2\. 独立微调的模型无法利用来自相关任务的信息，这限制了它们在领域内和领域外任务中的泛化能力。然而，多任务学习需要访问每个特定任务的数据集，且整合这些数据集可能会变得复杂。如果我们无法访问所有下游任务的数据集，但有可用的微调模型，该怎么办？假设你需要一个在一组特定任务上微调的大型语言模型（LLM）。与其收集大量下游任务的数据集并经历资源密集的微调过程，你可以找到已在每个任务上微调的LLM模型，并将这些模型合并成所需的模型。需要注意的是，在庞大的Hugging Face仓库中，寻找这样的模型并不困难，该仓库托管着大约50万个微调模型。近年来，合并多个模型已受到广泛关注，主要是因为它要求计算资源少，且不需要训练数据。

![](../Images/25ee63a959506e2d9587b14a354f7621.png)

图1 模型集成通过结合多个模型的输出提高准确性，但需要更多的计算资源。多任务学习同时在多个任务上训练一个模型，要求访问所有数据集并具备高计算能力。而模型合并则将预训练的模型融合为一个，利用它们的优势，计算量小且无需额外的训练成本，提供了一种高效的解决方案（图片来源：[论文](https://arxiv.org/pdf/2212.09849)）。

随着对模型合并的关注度不断增长，公共库如WEBUI和MergeKit也应运而生，以便简化这一过程。WebUIs使得合并微调过的模型（如Stable Diffusion）成为可能，且支持多种合并技术。MergeKit是一个开源的集中式库，提供不同的合并方法。它通过高效的合并技术实现，支持在任何硬件上进行模型合并。

> 在这里，我们将合并方法分为三大类：
> 
> 1\. 合并具有相同架构和初始化的模型，
> 
> 2\. 合并具有相同架构但初始化不同的模型，
> 
> 3\. 合并具有不同架构的模型。
> 
> 每个类别涉及不同的技术来有效地合并模型，下面将进行详细解释。

# **1\. 合并具有相同架构和初始化的模型：**

***1.a 无需数据的合并：***

本节中的模型合并方法都基于线性模式连接（[**LMC**](https://proceedings.neurips.cc/paper_files/paper/2019/file/05e97c207235d63ceb1db43c60db7bbb-Paper.pdf)）。LMC表明，对于具有相同架构和初始化的模型，它们的检查点之间的损失可以通过一条低损失的线性路径连接。这意味着这些模型可以通过线性插值进行合并。

为了微调一个模型，可以应用不同的配置，如不同的学习率、随机种子和数据增强技术，这些都会导致不同的模型参数。[**模型汤**](https://proceedings.mlr.press/v162/wortsman22a/wortsman22a.pdf)建议对这些参数进行平均化，因为这些模型已经学习到了相似的表示，并且在参数空间中彼此接近。加权模型平均可以得到一个平坦的局部最优解，并且对超出分布的任务具有更好的泛化能力[参见[13](https://proceedings.neurips.cc/paper_files/paper/2022/file/69b5534586d6c035a96b49c86dbeece8-Paper-Conference.pdf), [14](https://proceedings.neurips.cc/paper_files/paper/2021/file/995f5e03890b029865f402e83a81c29d-Paper.pdf)]。

![](../Images/003054738dc6d8ecf7c47c2b93be6c72.png)

图 2 Pl 显示了模型汤合并的结果，而 Ps 显示了 SLERP 合并的结果（图片由作者提供）。

**SLERP**（球面线性插值，首次介绍见[此处](https://dl.acm.org/doi/pdf/10.1145/325334.325242)）是一种在计算机图形学和动画中常用的技术，用于在由四元数表示的旋转之间平滑插值。SLERP 也适用于模型合并。它通过沿球面路径插值来合并两组模型参数，而不是沿直线路径。图 2 显示，对于给定的两个模型参数 p1 和 p2，SLERP 沿着地球表面合并这些参数，提供平滑的过渡。这种方法常用于合并大型语言模型（LLMs）。

假设给定两个多层感知机（MLP）模型，每个模型都在不同的下游任务上进行了微调。SLERP 可以通过以下步骤合并这两个模型：

**步骤 1**：对于每个模型参数，将其展平并连接成向量 v1、v2

**步骤 2**：将向量 v1​ 和 v2 正规化到单位超球面上（得到 v1′​ 和 v2′​）。

**步骤 3**：计算这两个向量之间的角度 θ（以弧度为单位）。

**步骤 4**：使用 SLERP 公式计算 Vslerp​：

![](../Images/c155fcd8531a3c30355f80f9cceb40ac.png)

其中，t 是插值参数，当 t=0 时仅使用模型 1，而 t=1 时仅使用模型 2。

线性加权平均技术，如模型汤和 SLERP，在计算机视觉领域中已广泛应用，从图像处理和分类模型到图像生成模型，如潜在扩散模型。

[**任务算术**](https://arxiv.org/pdf/2212.04089)提出了一种基于任务向量的方法。任务向量通过从同一模型的预训练权重（θpre​）中减去针对特定任务微调后的权重（θft​）来计算，公式如下：

τ = θft − θpre​。这个向量表示一个方向，在预训练模型的权重空间中，沿着该方向移动能够提高该任务的性能。任务向量可以通过算术运算，如取反和加法，进行组合。取反任务向量 (θpre — τ) 会减少模型在目标任务上的性能（遗忘），对控制任务的影响最小。为了提高预训练模型在多个任务上的表现，我们可以为每个任务最初学习一个任务向量。然后通过将这些任务向量相加 (θpre+∑τi)，我们可以提升模型同时处理多个任务的能力。

[**TIES**](https://proceedings.neurips.cc/paper_files/paper/2023/file/1644c9af28ab7916874f6fd6228a9bcf-Paper-Conference.pdf) 解决了在合并任务向量 (∑τi​) 时由于参数干扰而导致的性能下降问题。这个问题可以通过以下三步解决（见图 3）：

(1) 修剪每个任务向量至最大幅度的前 k%（通常 k = 20），

(2) 对于每个非零参数，选择所有任务向量中总幅度最大的符号，以避免冲突的变化，且

(3) 仅合并来自与选定符号相同的任务向量的值。

![](../Images/a397b5a6e7f5ac9b0e37f32f681f01be.png)

图 3 TIES 涉及的步骤示意图。模型中的每个参数被表示为一个方框。箭头表示由在不同任务上微调所产生的更新（任务向量，τ）对参数的影响，箭头的方向表示符号，长度表示幅度。1- 根据幅度修剪任务向量的值，2- 通过解决符号冲突，选择每个参数的符号（γm，绿色向量包含 +1 或 −1），3- 仅选择与选定符号对齐的值，并取它们的平均值作为最终的参数值。（图像来自 [论文](https://proceedings.neurips.cc/paper_files/paper/2023/file/1644c9af28ab7916874f6fd6228a9bcf-Paper-Conference.pdf)）

[**DARE**](https://openreview.net/pdf?id=fq0NaiU8Ex) 主要集中于LLM模型的融合，并识别任务向量中的极端冗余（τ = θft−θpre）。它提出了一个三步法：

1- 随机丢弃 p%（通常 p = 90）任务向量的值，

2- 将其余参数按 1/(1 − p) 的因子进行重新缩放，且

3- 合并 (θpre + λi ∑τi)

其中 λi 是缩放项，表示每个任务向量在合并时的重要性。

***1.b 与数据需求的融合：***

我们上面讨论的合并方法不需要数据。然而，也有一些方法确实需要数据来确定合并参数的最优权重。这些方法通过使用数据计算激活值，然后相应地调整权重。

其中一种方法是[**费舍尔合并**](https://arxiv.org/pdf/2111.09832)。给定K个微调模型，每个模型在不同的下游任务上进行训练，起始点是特定的预训练检查点，费舍尔合并对每个模型的参数进行加权求和。权重是通过费舍尔信息矩阵计算的，矩阵构造需要每个任务的数据。

在相关的发展中，[**RegMean**](https://arxiv.org/pdf/2212.09849)通过将模型合并任务重新表述为线性回归问题，显著超越了费舍尔加权合并。该方法为线性层的权重推导出闭式解，并均匀插值其他权重（如层归一化和偏置项）。给定K个微调模型和一些数据Xi，i=1,..,K，对于每个任务，可以按如下方式确定合并模型的线性层：

![](../Images/9ec5319efad0b1e71fc3d9fb24cce6ad.png)

其中Wi是来自第i个微调模型的线性层。

# **2\. 合并具有相同架构但不同初始化的模型**

给定具有相同架构和训练数据集但不同初始化的模型，像线性模型组合这样简单的合并方法通常无法很好地执行。主要原因是模型的权重没有对齐。因此，研究人员开发了利用神经网络置换对称性的技术。通过重新排列模型的神经元，可以使它们的权重更好地对齐，从而使合并过程更加有效。

[**Git-Rebasin**](https://arxiv.org/pdf/2209.04836)建议置换一个模型的权重以匹配另一个模型的配置。假设给定两个模型A和B，它们具有相同的架构和训练数据集，但初始化和训练数据的顺序不同。每个网络的权重可以进行置换而不改变其功能，这意味着交换隐藏层中的神经元可以得到功能等效的模型。

他们将此问题表述为一个优化问题，以识别跨层单元的最佳置换，从而在权重空间中对齐两个模型的参数。这种对齐确保了模型处于损失景观中的相似“盆地”，从而导致平滑且有效的合并。为了达到这一目标，Git-Rebasin提出了以下三个步骤：

1\. 对于每一层，寻找最佳置换的问题被公式化为线性分配问题（LAP）。这一步骤涉及计算激活矩阵，并找到对齐激活的最优置换矩阵。

2\. 给定所有层的最优置换，模型B的权重将被置换。

3\. 模型B的置换权重与模型A的权重之间的线性模型组合位于损失景观中的低损失区域，这确保了合并后的模型表现良好。

[**REPAIR**](https://arxiv.org/pdf/2211.08403)解决了Rebasin合并方法中的一个关键问题——方差崩溃问题，其中隐藏单元的激活方差显著小于原始网络相应单元的激活方差，尤其是在它们被插值之前。因此，神经元的激活在更深层次上几乎变得恒定，导致网络无法区分输入。REPAIR通过重新缩放插值网络的激活值，使其与原始网络的统计属性匹配，从而解决了这个问题。通过调整激活的均值和方差，插值网络保持了其各层的功能变异性。应用REPAIR方法显著降低了插值壁垒，提升了插值模型的性能。

# 3\. **合并具有不同架构的模型**

与目前讨论的方法相比，[**Frankenmerging**](https://github.com/arcee-ai/mergekit)并不会将多个模型合并为一个模型，而是将不同模型的不同层按顺序堆叠。因此，它能够合并具有不同架构的模型。

例如，为了构建一个具有40层的LLM，可以将第一个LLM的前24层堆叠到另一个LLM的第25到40层。这种方法在计算机视觉中的风格迁移中得到了广泛关注。尽管需要大量的反复试验和实验，但它已经带来了如Goliath和Solar-10.7B等令人印象深刻的LLM模型[见[此处](https://huggingface.co/models?sort=downloads&search=franken)]。

![](../Images/5b75c40130d9085905106d79368ab407.png)

图4 进化优化方法概述（图像来自[论文](https://arxiv.org/pdf/2403.13187)）。

[**进化优化**](https://arxiv.org/pdf/2403.13187)提出了一种框架，旨在自动合并给定的一组基础模型，使得合并后的模型在给定集合中超过任何单个模型的表现。该方法包括两个主要阶段（见图4）：

在第一阶段，该方法使用TIES-Merging与DARE结合进行N个基础模型的逐层合并。该过程通过使用进化算法进行优化，算法由特定任务的度量标准指导（例如，MGSM的准确度、VQA的ROUGE得分）。为了寻找未知变量，如DARE中的丢弃率百分比以及在合并过程中每个模型参数的权重，进化优化从一组可能的解开始，并随着时间的推移不断演化。通过变异（小的随机变化）和交叉（组合两个解的部分），选择最优解来生成新的候选解组。这一迭代过程带来了逐步更好的解决方案。

在第二阶段，给定一组 N 个模型，目标是通过 Frankenmerging 找到一个具有 T 层的最优模型。为了减少搜索空间并使优化过程可行，所有层按顺序排列（即第 i 个模型中的所有层，紧接着是第 i+1 个模型中的层），并重复 r 次。在此阶段，目标是找到一个最优指示符，确定是否包含/排除某些层：如果 Indicator(i) > 0，则第 i 层包含在合并后的模型中；否则，排除该层。

**EvolutionaryOptimization** 过程从将第一阶段应用于一组模型开始。然后，将第一阶段的合并模型添加到给定集合中，并在这个扩展的集合上应用第二阶段，找到一个最优指示符，选择 T 层作为最终合并模型的层。此方法应用于将一个日语LLM与英语数学LLM合并，构建日语数学LLM。合并后的模型在多个已建立的日语LLM基准上取得了最先进的性能，甚至超过了具有显著更多参数的模型，尽管该模型并未针对这些任务进行训练。

> 本博文中表达的观点仅代表我们个人的意见，并不反映我们雇主的立场。
> 
> ***另请阅读我们之前的文章：*** [从单模态到多模态：构建基础模型的DIY技术](/from-open-source-unimodal-to-multimodal-diy-techniques-for-building-foundational-models-e1df92276379)

***参考文献：***

[1] [**Model soup**](https://proceedings.mlr.press/v162/wortsman22a/wortsman22a.pdf)**:** Wortsman, Mitchell, 等人。“模型汤：平均多个微调模型的权重可以提高准确性而不增加推理时间。” *（2022年）。

[2] [**Task arithmetic**](https://arxiv.org/pdf/2212.04089)**:** Ilharco, Gabriel, 等人。“通过任务算术编辑模型。”（2022年）。

[3] [**TIES**](https://proceedings.neurips.cc/paper_files/paper/2023/file/1644c9af28ab7916874f6fd6228a9bcf-Paper-Conference.pdf)**:** Yadav, Prateek, 等人。“Ties-merging：在合并模型时解决干扰问题。”（2024年）。

[4] [**DARE**](https://openreview.net/pdf?id=fq0NaiU8Ex)：Yu, Le, 等人。“语言模型就是超级马里奥：从同源模型中吸收能力作为免费午餐。” *（2024年）。

[5] [**Fisher Merging**](https://arxiv.org/pdf/2111.09832)Matena, Michael S., 等人。“通过Fisher加权平均合并模型。”（2022年）。

[6] [**RegMean**](https://arxiv.org/pdf/2212.09849)**:** Jin, Xisen, 等人。“通过合并语言模型的权重实现无数据知识融合。”（2022年）。

[7] [**Git-Rebasin**](https://arxiv.org/pdf/2209.04836)**:** Ainsworth, Samuel K., 等人。“Git re-basin：合并模型时考虑置换对称性。”（2022年）。

[8] [**REPAIR**](https://arxiv.org/pdf/2211.08403)**:** Jordan, Keller, 等人。“Repair：重新归一化置换激活以进行插值修复。”（2022年）。

[9] [**弗兰肯合并**](https://github.com/arcee-ai/mergekit)**：** Charles O. Goddard. 2024\. mergekit.

[10] [**进化优化**](https://arxiv.org/pdf/2403.13187)**：** Akiba, Takuya 等人。“模型合并方案的进化优化。”（2024）。

[11] Shoemake, Ken. “[使用四元数曲线进行旋转动画](https://dl.acm.org/doi/pdf/10.1145/325334.325242)。”（1985）。

[12] [**LMC**](https://proceedings.neurips.cc/paper_files/paper/2019/file/05e97c207235d63ceb1db43c60db7bbb-Paper.pdf)**：** Nagarajan, Vaishnavh 等人。“均匀收敛可能无法解释深度学习中的泛化。”（2019）。

[13] Kaddour, Jean 等人。“[平坦最小值优化器何时有效](https://proceedings.neurips.cc/paper_files/paper/2022/file/69b5534586d6c035a96b49c86dbeece8-Paper-Conference.pdf)？”（2022）

[14] Petzka, Henning 等人。“[相对平坦性与泛化](https://proceedings.neurips.cc/paper_files/paper/2021/file/995f5e03890b029865f402e83a81c29d-Paper.pdf)。”（2021）
