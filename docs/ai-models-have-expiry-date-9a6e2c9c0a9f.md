# AI 模型有过期日期——持续学习可能是答案

> 原文：[`towardsdatascience.com/ai-models-have-expiry-date-9a6e2c9c0a9f?source=collection_archive---------5-----------------------#2024-07-26`](https://towardsdatascience.com/ai-models-have-expiry-date-9a6e2c9c0a9f?source=collection_archive---------5-----------------------#2024-07-26)

## 为什么在这个唯一不变的就是变化的世界里，我们需要一种**持续学习**的方法来处理 AI 模型。

[](https://alicjadobrzeniecka.medium.com/?source=post_page---byline--9a6e2c9c0a9f--------------------------------)![Alicja Dobrzeniecka](https://alicjadobrzeniecka.medium.com/?source=post_page---byline--9a6e2c9c0a9f--------------------------------)[](https://towardsdatascience.com/?source=post_page---byline--9a6e2c9c0a9f--------------------------------)![Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9a6e2c9c0a9f--------------------------------) [Alicja Dobrzeniecka](https://alicjadobrzeniecka.medium.com/?source=post_page---byline--9a6e2c9c0a9f--------------------------------)

·发布于 [Towards Data Science](https://towardsdatascience.com/?source=post_page---byline--9a6e2c9c0a9f--------------------------------) ·阅读时长 7 分钟·2024 年 7 月 26 日

--

![](img/a30f4c8693a444a23e937f1d298ffc5c.png)

图片由作者在 Midjourney 生成

想象一下，你有一个小型机器人，它被设计用来在你的花园里走动并浇水。最初，你花费了几周的时间收集数据来训练和测试机器人，投入了相当多的时间和资源。当花园的地面覆盖着草和裸土时，机器人学会了高效地导航。

然而，随着时间的推移，花朵开始盛开，花园的外观发生了显著变化。这个根据不同季节的数据训练出来的机器人，现在无法准确识别周围的环境，并且难以完成任务。为了修复这个问题，你需要将盛开的花园的新例子添加到模型中。

你最初的想法是向训练中添加新的数据样本，并重新从头开始训练模型。但这是昂贵的，而且你不想每次环境变化时都这样做。此外，你刚刚意识到，你没有所有历史训练数据可用。

现在你考虑只用新样本微调模型。但这样做是有风险的，因为模型可能会失去之前学到的一些能力，从而导致**灾难性遗忘**（即模型在学习新信息时，失去之前获得的知识和技能）。

..那么有没有替代方法呢？有的，使用持续学习！

当然，机器人在花园里浇水只是问题的一个示例。在接下来的部分，你将看到更现实的应用场景。

## 使用持续学习（CL）进行适应性学习

无法预见并为模型未来可能遇到的所有场景做好准备。因此，在许多情况下，随着新样本的到来，对模型进行自适应训练可能是一个不错的选择。

在持续学习（CL）中，我们希望找到模型**稳定性**与**可塑性**之间的平衡。稳定性是指模型保持之前学习的信息的能力，而可塑性是指模型随着新任务的引入能够适应新信息的能力。

> *“(…) 在持续学习场景中，需要一个学习模型能够随着任务分布在其生命周期中动态变化，逐步建立和动态更新内部表示*。” [2]

## 那么，如何控制稳定性与可塑性呢？

研究人员已经确定了多种构建自适应模型的方法。在[3]中，已建立了以下几类方法：

1.  **基于正则化的方法**

+   在这种方法中，我们添加一个正则化项，用以平衡新任务与旧任务对模型结构的影响。

+   **例如，权重正则化**旨在通过向损失函数添加一个惩罚项来控制参数的变化，该惩罚项会根据参数对之前任务的贡献程度惩罚其变化。

**2. 基于重放的方法**

+   这一组方法的重点是恢复一些历史数据，使得模型仍然能够可靠地解决以前的任务。该方法的一个局限性是我们需要访问历史数据，而这并非总是可能的。

+   **例如，经验重放**，即我们保存并重放一部分旧的训练数据。在训练新任务时，添加一些来自旧任务的样本，以将模型暴露于旧任务与新任务类型的混合，从而限制灾难性遗忘。

**3. 基于优化的方法**

+   在这里，我们希望调整优化方法，以保持所有任务的性能，同时减少灾难性遗忘的影响。

+   **例如，梯度投影**是一种方法，其中计算的新任务的梯度会被投影，以避免影响先前的梯度。

**4. 基于表示的方法**

+   这一组方法的重点是获得并使用强健的特征表示，以避免灾难性遗忘。

+   **例如，自监督学习**，即模型可以在专门训练特定任务之前，学习数据的强健表示。其理念是学习能够反映良好泛化能力的高质量特征，以应对模型未来可能遇到的不同任务。

**5. 基于架构的方法**

+   之前的方法假设使用单一模型和单一参数空间，但在持续学习中，也有许多技术可以利用模型的架构。

+   **例如，参数分配，** 在训练过程中，每个新任务都会在网络中分配一个专用的子空间，这解决了参数冲突干扰的问题。然而，如果网络不是固定的，其规模会随着新任务数量的增加而增长。

## 那么，如何评估 CL 模型的性能呢？

CL 模型的基本性能可以从多个角度来衡量[3]：

+   **总体性能评估：** 所有任务的平均性能

+   **记忆稳定性评估：** 计算给定任务在持续训练前的最大性能与当前性能之间的差异

+   **学习可塑性评估：** 衡量联合训练性能（如果在所有数据上训练）与使用 CL 训练时的性能差异

## 那么，为什么并不是所有 AI 研究人员都立刻转向持续学习呢？

如果你可以访问历史训练数据并且不担心计算成本，那么从头开始训练似乎更容易。

其中一个原因是，模型在持续训练过程中发生的事情的可解释性仍然有限。如果从头开始训练能够得到与持续训练相同或更好的结果，那么人们可能更倾向于选择更简单的方法，即从头开始重新训练，而不是花时间去理解 CL 方法的性能问题。

此外，当前的研究往往集中在模型和框架的评估上，这可能无法很好地反映出商业可能面临的实际使用案例。正如[6]中所提到的，有许多合成增量基准测试并没有很好地反映出现实世界中任务自然演变的情况。

最后，正如在[4]中所指出的，许多关于 CL 的论文关注的是存储问题而非计算成本，实际上，存储历史数据比重新训练模型要便宜且耗能更少。

> 如果更多地关注模型重新训练中计算和环境成本的包含，可能会有更多的人对改进当前的 CL 方法感兴趣，因为他们会看到可衡量的收益。例如，正如[4]中提到的，模型重新训练可能超过**10,000 GPU 天**的训练时间，尤其是在最近的大型模型中。

## 为什么我们需要致力于改进 CL 模型？

持续学习旨在解决当前 AI 模型最具挑战性的瓶颈之一——数据分布随时间变化的事实。重新训练成本高，且需要大量计算，这在经济和环境角度来看并不是一种可持续的方法。因此，在未来，发展完善的 CL 方法可能使得模型变得更加可接入且可重用，供更广泛的社区使用。

正如在[4]中发现并总结的那样，有一系列应用天生需要或能够从完善的 CL 方法中受益：

1.  **模型编辑**

+   在不破坏模型其他部分的情况下，选择性地编辑模型中易出错的部分。持续学习技术可以帮助我们在较低的计算成本下持续纠正模型错误。

**2\. 个性化与专业化**

+   通用模型有时需要进行调整，以便更好地个性化满足特定用户的需求。通过持续学习，我们可以只更新一小部分参数，而不会引入灾难性遗忘。

**3\. 设备端学习**

+   小型设备具有有限的内存和计算资源，因此能够在新数据到达时有效地实时训练模型，而无需从头开始的方法，在这一领域可能会非常有用。

**4\. 使用热启动进行更快速的再训练**

+   当新的样本可用或分布发生显著变化时，模型需要进行更新。通过持续学习，这一过程可以更高效地进行，只更新受新样本影响的部分，而不是从头开始重新训练。

**5\. 强化学习**

+   强化学习涉及智能体与通常是非静态的环境进行交互。因此，效率较高的持续学习方法和策略可能对这种使用场景有很大帮助。

## 了解更多

如你所见，**持续学习方法领域仍然有很大的提升空间**。如果你感兴趣，可以从以下材料开始：

+   入门课程：*【持续学习课程】第 1 讲：介绍与动机* 来自 ContinualAI 在 YouTube 上的视频 [`youtu.be/z9DDg2CJjeE?si=j57_qLNmpRWcmXtP`](https://youtu.be/z9DDg2CJjeE?si=j57_qLNmpRWcmXtP)

+   讲述持续学习动机的论文：*持续学习：应用与前进的道路* [4]

+   讲述持续学习最新技术的论文：*持续学习的综合调查：理论、方法与应用* [](https://arxiv.org/pdf/2302.00487) [3]

**如果你有任何问题或评论，请随时在评论区分享。**

干杯！

![](img/e1e87ef455f4ecbab117a49cda5e832d.png)

图片由作者在 Midjourney 生成

# 参考文献

[1] Awasthi, A., & Sarawagi, S. (2019). *使用神经网络的持续学习：综述*。发表于 ACM 印度联合国际数据科学与数据管理会议论文集（第 362-365 页）。计算机协会。

[2] Continual AI Wiki *持续学习简介* [`wiki.continualai.org/the-continualai-wiki/introduction-to-continual-learning`](https://wiki.continualai.org/the-continualai-wiki/introduction-to-continual-learning)

[3] Wang, L., Zhang, X., Su, H., & Zhu, J. (2024). **持续学习的综合调查：理论、方法与应用**。IEEE 模式分析与机器智能学报, 46(8), 5362–5383。

[4] Eli Verwimp, Rahaf Aljundi, Shai Ben-David, Matthias Bethge, Andrea Cossu, Alexander Gepperth, Tyler L. Hayes, Eyke Hüllermeier, Christopher Kanan, Dhireesha Kudithipudi, Christoph H. Lampert, Martin Mundt, Razvan Pascanu, Adrian Popescu, Andreas S. Tolias, Joost van de Weĳer, Bing Liu, Vincenzo Lomonaco, Tinne Tuytelaars, & Gido M. van de Ven. (2024). *持续学习：应用与未来发展之路* [`arxiv.org/abs/2311.11908`](https://arxiv.org/abs/2311.11908)

[5] Awasthi, A., & Sarawagi, S. (2019). 神经网络的持续学习：综述。收录于 *ACM 印度联合国际数据科学与数据管理会议论文集*（第 362–365 页）。计算机协会。

[6] Saurabh Garg, Mehrdad Farajtabar, Hadi Pouransari, Raviteja Vemulapalli, Sachin Mehta, Oncel Tuzel, Vaishaal Shankar, & Fartash Faghri. (2024). TiC-CLIP：CLIP 模型的持续训练。
