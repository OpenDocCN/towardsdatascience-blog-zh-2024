# 使用 SLERP 合并标记以加速 LLM 推理

> 原文：[https://towardsdatascience.com/merging-tokens-to-accelerate-llm-inference-with-slerp-38a32bf7f194?source=collection_archive---------9-----------------------#2024-04-19](https://towardsdatascience.com/merging-tokens-to-accelerate-llm-inference-with-slerp-38a32bf7f194?source=collection_archive---------9-----------------------#2024-04-19)

## 我们可以通过使用 SLERP 合并连续的标记对，显著加速 LLM 下一标记的生成，从而减少进行完整预测所需的计算能力。

[](https://medium.com/@sam.chaineau?source=post_page---byline--38a32bf7f194--------------------------------)[![Samuel Chaineau](../Images/9fcc6bac98b3089dc984c4e337083f07.png)](https://medium.com/@sam.chaineau?source=post_page---byline--38a32bf7f194--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--38a32bf7f194--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--38a32bf7f194--------------------------------) [Samuel Chaineau](https://medium.com/@sam.chaineau?source=post_page---byline--38a32bf7f194--------------------------------)

·发表于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--38a32bf7f194--------------------------------) ·6 分钟阅读·2024 年 4 月 19 日

--

![](../Images/1d20ac9c439c47bb8dac7cc59881664d.png)

图片由 [Martin Martz](https://unsplash.com/@martz90?utm_source=medium&utm_medium=referral) 提供，来源于 [Unsplash](https://unsplash.com/?utm_source=medium&utm_medium=referral)

# TL;DR：

本文介绍了一种通过使用球面线性插值（SLERP）合并标记来加速大型语言模型（LLM）推理的新方法。通过减少序列长度而保持质量，这项技术在 LLM 推理中提供了显著的速度提升，解决了较长序列带来的计算挑战。该方法仍然处于初步阶段，但揭示了 LLM 的双重世界：一个用于训练，另一个用于预测。

# 背景：

LLM 已经通过展现出卓越的生成能力，彻底改变了自然语言处理任务。然而，它们的有效性是有代价的——计算资源。随着 LLM 处理更长的序列，变换器计算的二次扩展变得越来越难以承受。传统的缓解方法，如缓存和量化，存在一定的局限性。因此，迫切需要创新的方法来加速 LLM 推理，而不至于过多损害质量。

当前在推理过程中生成token的方法是一种蛮力法，本质上是训练方法的转置。虽然这种方法在训练中已被证明有效，但在推理任务中可能并不是最有效的。因此，有必要开发一种专门用于推理过程中生成token的新方法，从而优化过程并进一步提高大语言模型（LLM）的效率。这突显了探索替代技术以应对LLM推理所面临的计算挑战的重要性。

最近，mergekit库提出了使用SLERP方法合并网络权重，这通常能得到更好的结果。受到这一工作的启发，我决定尝试是否可以在一个序列中合并token，从而生成一个更小的序列，以便在预测下一个token时处理。

![](../Images/1fcde1ba09eb3d7862d2b002b155125e.png)

普通生成与合并生成的对比

# 使用SLERP合并序列：

提出的这种方法涉及修改LLM的前向传播过程，通过球面线性插值（SLERP）合并token，这一技术借鉴自计算机图形学和动画制作。与简单的平均化技术不同，SLERP保留了token维度的球面特性，提供了更为精细的插值。合并过程包括几个步骤，用于高效地压缩输入序列：

**序列长度调整**：

最初，输入序列会根据其长度进行调整：

+   长度小于3的序列保持不变。

+   对于奇数长度的序列，添加两个空token，一个放在开头，一个放在结尾

+   偶数长度的序列会在倒数第二个位置添加一个空token。

这样做的目的是确保上下文中的第一个和最后一个token能够得到保留。

**对形成：**

调整后的序列将被格式化为一对对连续的token。这一过程为聚合做好准备。

**使用SLERP进行聚合：**

每对token都会通过SLERP进行聚合，实际上将序列长度减半（并不是完全减半，因为我们会添加并保留一些额外的token）。SLERP对代表连续token的两个向量进行插值，从而创建一个新的向量。

为了高效地完成这一任务，我重新创建了所有SLERP函数，并使用原生PyTorch实现。然而，代码可能尚未经过优化。

**层次切割和提示保留：**

合并过程可以在模型架构的不同层次上进行，这被称为“层次切割”。此外，为了保留提示的完整性，可以指定序列的开始部分和/或结束部分保持不变。这在基于指令的模型中尤为有用，因为提示的开始部分应该始终被记住。

这种创新方法为解决LLM推理中相关的计算挑战提供了一种微妙的解决方案，承诺在不牺牲质量或准确性的情况下大幅提高速度。

![](../Images/6934167f27afc797fc61f04a0cba74aa.png)

合并序列的简单示例

**这意味着什么？**

具体来说，在LLM中，前向调用的输入是一个形状为（batch_size，序列长度）的令牌序列。嵌入层生成一个形状为（batch size，序列长度，维度）的序列。每个注意力模块将这个序列作为输入。在给定的注意力层中，你可以合并令牌，生成一个形状为（batch size，k，维度）的序列，其中k是压缩后的序列长度。选择在哪一层应用此操作就是“层级截止”。

接下来的注意力模块将不再需要计算一个（序列长度，序列长度）的注意力矩阵，而是一个更小的矩阵，因为k严格小于原始序列长度。

因此，合并可以发生在模型架构的不同层级。这个参数被称为“层级截止”。此外，为了确保提示语不会完全合并，你可以定义序列的部分部分在开始和/或结束时保持不变。对于基于Instruct的模型，这种方式更加高效，因为提示的起始部分应该始终被记住。

这种方法论的一个缺点是它严重依赖于所使用模型的前向传播过程，这要求你根据所选择的模型仔细重写“合并”过程。另一个缺点是每一步都需要重新计算注意力掩码，并可能需要重新计算位置嵌入。

# 结果：

对Mistral 7B Instruct V0.2模型进行的实验展示了有前景的结果。通过比较基础模型与不同层级截止的各种合并模型之间的预测，可以观察到，合并令牌对预测质量的影响不大。此外，合并模型在推理时间上表现出了显著的加速，特别是在较浅的层级上。该技术还展示了其在处理更长序列时的有效性，使其适用于各种应用场景。

![](../Images/31775627fd11cb17bd858d233710c1dc.png)

在不同层级截止和序列长度下，合并推理模型的预测令牌与基础模型的准确性对比

![](../Images/68a9ee0c7d5e691ed29b3d5bdd142300.png)

对于不同的序列长度和层级截止，合并推理模型相比基础模型的加速倍数

一个缺点是我未能成功使前向调用达到最优。因此，可能通过重新思考过程，能找到许多优化的空间。

我还在AlpacaEval数据集上测试了Mistral Instruct v0.2的合并版本。我在第20个注意力模块上进行合并。结果非常令人鼓舞，因为该模型超过了Falcon 7B、Gemma 7B和nous-hermes-13b。它表明，未重新思考位置编码的合并模型生成的平均令牌数增加了600个，说明该模型的表现更好，*更具表现力*。我尝试重新实现位置编码过程，但没有成功。

![](../Images/e8f72feb762d2920456495788e6525e9.png)

从排行榜中提取

总结来说，使用SLERP合并tokens是应对LLM推理计算挑战的一个强有力的解决方案。通过在速度和质量之间找到平衡，这种方法几乎只是重写了前向循环。

# 使用方法：

我准备了一个仓库，包含一个简单的笔记本，供大家体验：[https://github.com/samchaineau/llm_slerp_generation](https://github.com/samchaineau/llm_slerp_generation)

使用一个新类，适配了前向调用，你可以轻松地将LLM传递到生成管道，并在你的数据集上使用它。到目前为止，我的实验仅限于Mistral 7B模型，但我希望将其扩展到其他架构，看看性能是否能保持。

所有资源都已经上传，如果你希望在其他LLM上进行测试，可以随时联系我。

# 结论：

使用SLERP技术合并tokens应该被探索用于加速LLM推理。随着进一步优化和探索，它有潜力提高自然语言处理任务的效率和可扩展性。

如果你从事AI领域工作并希望将其提升到下一个水平：欢迎联系我！

Github链接：[https://github.com/samchaineau/llm_slerp_generation](https://github.com/samchaineau/llm_slerp_generation)

HuggingFace个人主页：[https://huggingface.co/samchain](https://huggingface.co/samchain)

# 相关且启发性的作品：

- Token Merging Stable Diffusion（论文）：[https://arxiv.org/abs/2303.17604](https://arxiv.org/abs/2303.17604)

- Token Merging Stable Diffusion（库）：[https://huggingface.co/docs/diffusers/optimization/tome](https://huggingface.co/docs/diffusers/optimization/tome)

- Token Merging NLP（论文）：[https://llm-random.github.io/posts/mixture_of_tokens/](https://llm-random.github.io/posts/mixture_of_tokens/)

除非另有说明，所有图片均由作者提供。
