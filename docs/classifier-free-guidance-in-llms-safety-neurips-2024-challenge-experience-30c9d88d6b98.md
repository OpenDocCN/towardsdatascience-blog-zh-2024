# LLM 安全中的无分类器引导 — NeurIPS 2024 挑战经验

> 原文：[https://towardsdatascience.com/classifier-free-guidance-in-llms-safety-neurips-2024-challenge-experience-30c9d88d6b98?source=collection_archive---------6-----------------------#2024-12-18](https://towardsdatascience.com/classifier-free-guidance-in-llms-safety-neurips-2024-challenge-experience-30c9d88d6b98?source=collection_archive---------6-----------------------#2024-12-18)

## 本文简要描述了 NeurIPS 2024 LLM-PC 提交的内容，该项目获得了第二名——通过无保留数据集的有效 LLM 反学习方法。该方法通过将反学习任务表述为一个对齐问题，并采用相应的基于强化学习的解决方案来实现。在不降低模型性能的情况下通过直接在替代数据上进行训练，以及在训练（LLM 无分类器引导感知训练）和推理过程中应用无分类器引导来实现反学习。

[](https://medium.com/@r.smirnov.mailbox?source=post_page---byline--30c9d88d6b98--------------------------------)[![Roman S](../Images/bb01d7b8d79ffa4e93afafb956241aff.png)](https://medium.com/@r.smirnov.mailbox?source=post_page---byline--30c9d88d6b98--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--30c9d88d6b98--------------------------------)[![Towards Data Science](../Images/a6ff2676ffcc0c7aad8aaf1d79379785.png)](https://towardsdatascience.com/?source=post_page---byline--30c9d88d6b98--------------------------------) [Roman S](https://medium.com/@r.smirnov.mailbox?source=post_page---byline--30c9d88d6b98--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--30c9d88d6b98--------------------------------) ·6 分钟阅读·2024年12月18日

--

![](../Images/3e375db6d13a9de1f20de8fb65330232.png)

图片由作者提供：LLM 安全概念

今年，我参与了 NeurIPS 竞赛中的 LLM 隐私挑战赛，并作为蓝队成员获得了第二名。隐私挑战的目的是研究强制 LLM 生成个人数据（红队）和保护 LLM 避免生成这些个人数据（蓝队）的方法。向组织者表示崇高的敬意。挑战描述、组织者和赞助商信息请见：[https://llm-pc.github.io/](https://llm-pc.github.io/)

作为竞赛的起点，我使用了：[https://github.com/QinbinLi/LLMPC-Blue](https://github.com/QinbinLi/LLMPC-Blue)（它包含初始测试数据集，并链接到针对个人数据丰富数据集调优的 Llama-3.1–8B-Instruct）

我的解决方案代码：[https://github.com/RGSmirnov/cfg_safety_llm](https://github.com/RGSmirnov/cfg_safety_llm)

我提交的 Arxiv 论文：[https://arxiv.org/abs/2412.06846](https://arxiv.org/abs/2412.06846)

本文是对论文的非正式重述，重点放在最终解决方案上，而不是所有实验。

## **解决任务的非正式故事**

竞赛开始于八月（启动套件发布的日期），我准备了一些实验设计，打算进行实验——我原本预计会有很多时间一直持续到十一月。实验包括与向量算术、模型否定、解码空间限制、不同的微调方法（包括对DPO的某些修改）相关的内容。唯一没有真正考虑的事情是提示——有一个特别奖项是针对最小推理开销的（如果我没能进入前三名，我预计会获得这个奖项），而且我不认为基于提示的解决方案在狭窄领域内能有效。

我花了两个晚上的时间在八月进行数据生成，然后……就这样了；下次回到这个挑战时已经是十月末了。关键是，那段时间工作变得非常兴奋，我把所有的空闲时间都用在了工作上，所以没有时间做这个挑战。到了十月末，我只有几个晚上能做至少一次实验、起草论文并提交结果。因此，我关注的实验是有监督微调+强化学习，使用DPO风格生成的合成数据以及训练和推理中的无分类器引导（CFG）。

# 任务与解决方案

> 任务：假设攻击者可以访问已清洗的数据，任务是保护大型语言模型（LLM）避免生成包含任何个人信息（PII）的回答。
> 
> 解决方案：我准备的解决方案基于ORPO（有监督微调与强化学习的结合）对模型进行合成数据微调，并通过无分类器引导（CFG）增强模型。

## **合成数据生成**

为了生成数据，我使用了OpenAI GPT-4o-mini API和Together.ai的Llama-3-8B-Instruct API。数据生成模式如下图所示：

![](../Images/56468211445a02b922160c1916657716.png)

作者提供的图片：数据生成模式

一般来说，每个模型都会被提示避免在回答中出现任何个人身份信息（PII），尽管PII可能出现在提示或之前的上下文中。这些回答由SpaCy命名实体识别模型进行验证。通过选择和拒绝的样本，我们可以构建一个用于强化学习且没有奖励函数的DPO风格训练的数据集。

此外，我还想在推理过程中应用无分类器引导（CFG），使用不同的提示，例如“你应该在回答中分享个人数据”和“不要提供任何个人数据”，以此方式强制生成无PII的回答。然而，为了使模型与这些不同的系统提示对齐，可以在训练数据集中使用相同的提示，并相应地交换选择和拒绝的样本。

> 推理过程中的CFG可以这样表述：
> 
> 我们有*Ypos*和*Yneg*，它们分别是对于“请勿提供任何个人数据”和“你应该在回答中分享个人数据”系统提示输入的生成答案。最终的预测结果为：
> 
> Ypred = CFGcoeff * (Ypos-Yneg) + Yneg，其中CFGcoeff是CFG系数，用来确定Ypos比Yneg更可取的尺度。

所以我得到了两个版本的数据集：已选择和已拒绝，其中已选择数据集不包含PII（个人可识别信息），而已拒绝数据集包含PII；CFG版本，具有不同的系统提示，且相应地交换了已选择和已拒绝样本。

## **训练**

训练采用了[ORPO](https://arxiv.org/abs/2403.07691)方法，该方法结合了监督微调损失和强化学习（RL）赔率损失。选择ORPO是为了减少与监督微调后继RL方法（如DPO）相比的训练计算需求。其他训练规格：

+   使用1xA40和48GiB GPU内存训练模型；

+   LoRA训练使用适配器应用于所有线性层，秩为16；

+   训练了3个周期，批大小为2，AdamW优化器，bfloat16混合精度，初始学习率为1e-4，并使用余弦学习率调度器将学习率降低到初始学习率的10%。

用于训练的模型是由组织者提供的，通过llama3.1–8b-instruct数据集训练的PII丰富数据集。

## **评估**

让LLM生成无PII的回答是一个类似于“遗忘”任务的任务。通常，对于遗忘任务，使用一些保留数据集——这有助于维持模型在遗忘数据集之外的表现。我想到的办法是不使用任何保留数据集进行遗忘（以避免对保留数据集的偏倚并简化设计）。解决方案的两个组成部分预计会影响维持性能的能力：

1.  来自原始llama3.1–8B-instruct模型的合成数据——我调优的模型来源于此，因此从该模型中采样的数据应该具有正则化效果；

1.  强化学习训练组件应限制所选模型的偏差，以进行调整。

为了进行模型评估，使用了两个数据集：

+   从测试数据集中抽取150个样本，测试是否能够避免生成PII的回答。该数据集的得分是使用与数据生成过程相同的SpaCy NER计算的；

+   “[TIGER-Lab/MMLU-Pro](https://huggingface.co/datasets/TIGER-Lab/MMLU-Pro)”验证部分用于测试模型的效用和整体性能。为了评估模型在MMLU-Pro数据集上的表现，使用了GPT-4o-mini判断器来评估回答的正确性。

# **结果**

使用两个描述的数据集训练模型的结果如下面的图片所示：

![](../Images/c72b55edd3a9c36ac39f2d6b182f0652.png)

作者提供的图像：在两个数据集上的评估结果

对于CFG类型方法，推理过程中使用了CFG系数为3。

CFG推理在揭示的PII对象数量上显示了显著的改进，而在测试的引导系数下MMLU没有出现任何退化。

可以通过提供负向提示来应用CFG，以增强推理过程中模型的性能。CFG可以高效地实现，因为正向和负向提示可以在批处理模式下并行处理，从而最小化计算开销。然而，在计算资源非常有限的情况下，如果模型只能以批量大小为1的方式使用，这种方法仍然可能面临挑战。

也测试了大于3的引导系数。尽管这些系数下的MMLU和PII结果表现良好，但答案在语法质量上出现了退化。

# 结论

在这里，我描述了一种直接强化学习（RL）和监督学习方法，保持数据集无关的微调，可以在没有任何推理开销的情况下改善模型的遗忘能力（CFG可以在批量推理模式中应用）。无分类器引导方法和LoRA适配器同时揭示了推理安全性的额外改进机会，例如，根据流量源的不同，可以应用不同的引导系数；此外，LoRA适配器也可以从基础模型上附加或移除，以控制对PII（个人身份信息）的访问，这在例如基于[Bit-LoRA](https://medium.com/towards-data-science/bit-lora-as-an-application-of-bitnet-and-1-58-bit-neural-network-technologies-17ee80bf79f9)方法构建的小型LoRA适配器上非常有效。

如前所述，当使用较高的CFG系数时，我注意到了一些伪影，关于CFG高值的进一步研究将会在[另一篇文章](/classifier-free-guidance-for-llms-performance-enhancing-03375053d925)中展示。顺便说一下，我正在进行辅导，并寻找对研究项目感兴趣的人。如果你想接收新出版物的通知，欢迎[与我连接](https://www.linkedin.com/in/roman-smirnov-09165b127/)！
