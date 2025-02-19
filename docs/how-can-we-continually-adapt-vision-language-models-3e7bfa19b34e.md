# 我们如何持续适应视觉-语言模型？

> 原文：[`towardsdatascience.com/how-can-we-continually-adapt-vision-language-models-3e7bfa19b34e?source=collection_archive---------6-----------------------#2024-08-26`](https://towardsdatascience.com/how-can-we-continually-adapt-vision-language-models-3e7bfa19b34e?source=collection_archive---------6-----------------------#2024-08-26)

## 探索 CLIP 的持续学习策略

[](https://alicjadobrzeniecka.medium.com/?source=post_page---byline--3e7bfa19b34e--------------------------------)![Alicja Dobrzeniecka](https://alicjadobrzeniecka.medium.com/?source=post_page---byline--3e7bfa19b34e--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--3e7bfa19b34e--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3e7bfa19b34e--------------------------------) [Alicja Dobrzeniecka](https://alicjadobrzeniecka.medium.com/?source=post_page---byline--3e7bfa19b34e--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--3e7bfa19b34e--------------------------------) ·8 分钟阅读·2024 年 8 月 26 日

--

![](img/50e5d7820bf844fb40605562495b7cb7.png)

图片由作者在 Midjourney 中创作

目前对大语言模型的研究和应用兴趣日益增长。然而，这些模型只能处理文本数据，这限制了它们在某些应用中的实用性。人类能够处理跨多个模态的信息，如书面和口语语言，以及对我们周围现实的视觉理解。我们期望模型能够进行类似的处理。

**视觉-语言模型**能够处理文本和视觉数据，这在图像分析（例如医学图像）、物体识别和更好的场景理解（例如自动驾驶汽车）、为图像生成描述、回答视觉问题、与图像进行对话等领域有广泛的应用。

> 不幸的是，多模态模型面临着与单模态模型相同的挑战。一旦训练完成，随着新数据样本的到来或数据分布的变化，它们可能会随着时间的推移变得过时。

在我的[上一篇文章](https://medium.com/towards-data-science/ai-models-have-expiry-date-9a6e2c9c0a9f)中，我介绍了**持续学习（CL）**方法，适用于 AI 模型。持续学习试图找到持续训练模型的方法，这可能是未来更可持续的解决方案。在本文中，我想探讨**将 CL 应用于视觉-语言模型（VLMs）**的可能性——特别是对比语言-图像预训练（CLIP）模型的应用。

## 但什么是 CLIP？

对比语言-图像预训练（CLIP）由 OpenAI 在 2021 年的 *从自然语言监督学习可转移的视觉模型* 论文中提出 [1]。

> CLIP 模型的目标是**理解文本与图像之间的关系**。如果你输入一段文本，它应该返回在给定图像集合中最相关的图像。同样，如果你输入一张图像，它应该从可用的文本集合中给出最匹配的文本。

CLIP 是在一个大型的文本-图像对数据集上训练的。通过对比学习，将匹配的文本-图像对拉近到嵌入空间中，并将不匹配的对远离彼此。然后，这个学习到的共享嵌入空间在推理时用于理解文本和图像之间的关系。如果你想了解更多关于 CLIP 的信息，我推荐阅读[以下文章](https://medium.com/towards-data-science/clip-intuitively-and-exhaustively-explained-1d02c07dbf40)，它详细描述了 CLIP。

## 为什么我们需要为视觉-语言模型进行持续学习？

大型基础模型可能会因为分布的变化或新数据样本的到来而变得过时。重新训练这些模型既昂贵又耗时。TiC-CLIP 论文的作者 [7] 显示，当前的评估实践往往未能捕捉到在考虑时间演变数据时性能的差异。

在图 1 中，你可以看到如果我们比较 2020 年前训练的 OpenAI 模型和 2022 年前训练的 OpenCLIP 模型，尽管它们在 Imagenet 上的鲁棒性（左侧图像）差异不大，但在从 2014-2016 年和 2021-2022 年的检索任务上比较时（右侧图像），它们之间存在性能差距，表明 OpenAI 模型在时间演变的数据上零-shot 鲁棒性较差 [7]。

![](img/ce1610a9e1bdb4c9f10f04d6a784a443.png)

图 1. 来自论文 TiC-CLIP: Continual Training of Clip Models [7] 的图像。

此外，持续学习可能是某些使用案例的自然选择，比如在线终身学习（OLL）[8]，其中数据来自于连续和非平稳的数据流，并随着时间变化而演化。

最后，正如 [4] 中指出的，CLIP 展示了显著的零-shot 能力，但对于某些领域，由于预训练时某些类别的数据不足，它可能难以实现良好的性能。

## 挑战

随着一些当前最先进的视觉-语言模型需要越来越多的计算时间和资源，找到一种不断适应这些模型而无需重新训练的方法似乎变得至关重要。然而，持续适应这些模型也面临一些挑战：

+   **灾难性遗忘——** 学习新任务可能会损害旧任务的性能。

+   **丧失零-shot 能力 —** 预训练模型可以表现出零-shot 行为，意味着它们可以执行没有接收过训练数据的任务，即在训练时未见过的图像类别的分类。这个能力在持续训练时可能会丧失。

+   **文本和图像表示之间的错位 —** 正如[12]的作者所指出的，在 CLIP 的持续学习过程中，多模态表示空间的对齐可能会退化，这可能导致长期的性能下降。

## CLIP 的持续学习方法

目前正在进行的研究旨在改善多模态模型的持续学习方面。以下是一些现有的策略和应用场景：

1.  **专家混合（MoE）**

+   为了持续训练 CLIP，[2]的作者提出了使用任务特定适配器的**MoE**方法。他们在冻结的 CLIP 模型上构建了一个动态扩展架构。

+   这里的想法是，随着新任务的训练，添加新的适配器。同时，训练分布判别自选器，以便在推理阶段，模型可以自动选择测试数据是否应进入 MoE 适配器，或进入预训练的 CLIP 进行零-shot 检测。

2\. **CoLeCLIP**

+   [4]的作者关注的是开放领域中视觉-语言模型的持续学习问题——在这些领域中，我们可能拥有来自不同已见和未见领域的包含新类的数据集。

+   解决开放领域挑战对于**AI 助手、自动驾驶系统和机器人**等应用场景尤为重要，因为这些模型在复杂且变化的环境中运行[4]。

+   **CoLeCLIP**基于 CLIP，但进行了调整以应对开放领域问题。

+   在 CoLeCLIP 中，每个任务都附加了一个外部可学习的参数高效微调（PEFT）模块，连接到 CLIP 的冻结文本编码器，以学习类的文本嵌入[4]。

3\. **持续语言学习（CLL）**

+   [3]的作者指出，当前的预训练视觉-语言模型通常只支持英语。同时，创建多语言模型的流行方法成本高昂，并且需要大量数据。

+   在他们的论文中，提出通过使用**CLL**来扩展语言能力，其中语言知识是增量更新的。

+   **CLL-CLIP**使用可扩展的嵌入层来存储语言差异。它仅训练令牌嵌入，并优化图像和多语言文本之间的对齐学习[3]。

+   作者还提出了一种新颖的方法，确保在初始化时，所有令牌嵌入的分布是相同的，并且在训练过程中得到了正则化。你可以在他们的论文中的图 2 看到这一过程的可视化。

![](img/532d655751f4c0832b09042cda678768.png)

图 2\. 来自论文《通过持续语言学习拥抱 CLIP 中的语言包容性和多样性》中的图像[3]。

4\. **对称图像-文本调优策略（SIT）**

+   在[8]中，作者观察到，在他们的在线终身学习场景中，进行参数高效调优（PET）时，文本与图像之间出现不对称，这可能导致灾难性遗忘。

+   他们提出使用 SIT 策略来缓解这个问题。该方法仅在在线学习期间，在当前批次内匹配图像和类别标签。

+   目标是在不引入编码器之间不对称性的情况下，保持 CLIP 的泛化能力，同时提高其在特定下游任务或数据集上的表现。

## 持续学习模型的评估

持续学习（CL）的评估标准似乎仍在完善中。许多现有的评估 CL 模型有效性的基准在构建数据集时并没有考虑时间因素。正如[7]中提到的，性能差距有时只有在我们重新创建时间演变的测试数据设置时才会显现出来。

此外，许多现有的视觉-语言模型基准仅关注单张图像输入，而未衡量多图像理解，这在某些应用中可能至关重要。[5]的作者开发了一个多图像评估基准，可以更细致地评估当前最先进模型的局限性和能力。

## 持续学习并不能解决所有问题…

像 CLIP 这样的视觉-语言模型有其不足之处。在[6]中，作者探讨了 CLIP 的视觉嵌入空间与纯粹的视觉自监督学习之间的差距。他们研究了嵌入空间中的错误匹配，在这些地方，图像的编码相似，尽管它们不应如此。

> 根据他们的结果可以得出结论，如果一个预训练模型存在弱点，那么在模型适应过程中，这些弱点可能会被传播。学习视觉表示仍然是一个开放的挑战，视觉模型可能会成为多模态系统的瓶颈，因为仅仅扩展模型规模并不能解决像 CLIP 这样的模型内在的局限性。[6]

## **结论**

本文探讨了将持续学习应用于视觉-语言模型的机遇与挑战，重点介绍了 CLIP 模型。希望本文能给您提供一个初步印象，表明虽然持续学习似乎是未来 AI 模型的一个良好方向，但仍有大量工作需要完成，才能使其完全可用。

**如果您有任何问题或评论，请随时在评论区分享。**

下次再见！

![](img/1fca5a8264874543865ae3508b2a7c78.png)

图像由作者在 Midjourney 中生成。

## 参考文献

[1] Radford, A., Kim, J., Hallacy, C., Ramesh, A., Goh, G., Agarwal, S., Sastry, G., Askell, A., Mishkin, P., Clark, J., Krueger, G., & Sutskever, I. (2021). 从自然语言监督中学习可转移的视觉模型。载于*第 38 届国际机器学习会议论文集*（第 8748–8763 页）。PMLR。

[2] 余家左, 朱戎智, 张璐, 胡平, 王东, 陆虎川, & 何友. (2024). 通过专家混合适配器提升视觉-语言模型的持续学习。

[3] 杨邦, 戴勇, 程旭鑫, 李耀伟, 阿西夫·拉扎, & 邹跃贤. (2024). 通过持续语言学习拥抱 CLIP 中的语言包容性和多样性。

[4] 李宇坤, 庞冠松, 苏伟, 景晨晨, 谷凌曦, 刘玲桥, 陈昊, 梁国强, & 王鹏. (2024). CoLeCLIP：通过联合任务提示和词汇学习实现开放域持续学习。

[5] 赵冰晨, 宗永硕, 张乐天, & 提莫西·霍斯佩达雷斯. (2024). 视觉和语言模型中的多图像理解基准测试：感知、知识、推理和多跳推理。

[6] 孙胜邦, 刘庄, 斋月翔, 马怡, 扬·勒昆, & 谢赛宁. (2024). 眼睛紧闭？探索多模态 LLM 的视觉缺陷。

[7] 索拉布·加尔格, 哈迪·普尔·安萨里, 梅赫达德·法拉吉塔巴尔, 萨钦·梅塔, 拉维特贾·维穆拉帕利, 恩切尔·图泽尔, 维沙尔·尚卡尔, & 法尔塔什·法赫里 (2023). TiC-CLIP：CLIP 模型的持续训练. 在*NeurIPS 工作坊*中。

[8] 王乐源, 向柳玉, 魏宇杰, 王云龙, & 何兆锋. (2024). CLIP 模型是一个高效的在线终身学习者。

[9] 维沙尔·腾甘, 萨尔曼·汗, 穆纳瓦尔·哈亚特, & 法赫德·汗. (2023). CLIP 模型是一个高效的持续学习者。

[10] 丁宇轩, 刘玲桥, 田春娜, 杨景元, & 丁昊轩. (2022). 别停下学习：面向 CLIP 模型的持续学习。

[11] 阿卡什·戈什, 阿尔卡迪普·阿查亚, 斯里帕尔娜·萨哈, 维尼娅·简, & 阿曼·查达. (2024). 探索视觉-语言模型的前沿：当前方法论与未来方向的调查。

[12] 倪子, 魏林, 唐爽, 庄煜, & 田琦. (2023). 通过离对角线信息进行持续的视觉-语言表示学习. 在*第 40 届国际机器学习会议论文集*中。JMLR.org。
